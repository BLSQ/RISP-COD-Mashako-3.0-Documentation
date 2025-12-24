**Purpose:** This notebook handles the final stage of the pipeline: exporting generated PDF reports to cloud storage and updating the central database so they can be accessed via the [user interface](https://pev-rdc.bluesquare.org/) for the [reports download](https://pev-rdc.bluesquare.org/#/reports).

### 1. Key Functions

- **Storage (S3 Upload)**: Connects to the [AWS S3 bucket](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/36be60fe-dad0-4b6a-9f68-cdd50df6c43a/) `cod-bmgf-risp-public` and uploads all PDF files found in the output directory (`.../out/reports/pdf/`).
- **Metadata Integration (DB Update)**: Updates the OpenHexA database table `public_reports`. It either inserts a new record for a new report or updates the `updated_at` timestamp if the report already exists.
- **Local Cleanup**: Moves the uploaded PDF files from the temporary output folder to a "fixed" directory (`produced_reports`) to prevent redundant uploads in future runs.

### 2. Dependencies & Connections

- **Input Data**:
    - **Local PDF Reports**: Found in `.../out/reports/pdf/`.
    - **Health Pyramid Metadata**: Relies on `pyramid_level3_antenne.csv` to map health zones to their respective "antennas" for database categorization (this file is produced by the [[(pipeline) Health Pyramids DHIS2]]).
- **Connections**:
    - **S3**: `cod-bmgf-risp-public` [connection](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/36be60fe-dad0-4b6a-9f68-cdd50df6c43a/) for file hosting.
    - **Postgres DB**: The OH workspace [database](https://app.openhexa.org/workspaces/cod-mashako-3-0/databases/) URL for updating the `public_reports` [table](https://app.openhexa.org/workspaces/cod-mashako-3-0/databases/public_reports/).

### 3. Database Schema Mapping

For every report uploaded, the notebook populates the `public_reports` [table](https://app.openhexa.org/workspaces/cod-mashako-3-0/databases/public_reports/) with the following information:

| **Column**              | **Value / Logic**                                         |
| ----------------------- | --------------------------------------------------------- |
| **report_name**         | Hardcoded as _"Mashako 3.0: Rapport de la Zone"_.         |
| **period**              | The `month_report` parameter (e.g., `202510`).            |
| **level 2**             | Hardcoded as _"kl Kwilu Province"_ (for the pilot phase). |
| **level 3**             | The Antenna name (retrieved from the pyramid file).       |
| **level 4**             | The Health Zone name.                                     |
| **report_download_url** | The public S3 link to the PDF.                            |

### 4. Technical Workflow

1. **S3 Upload**: Uses `fsspec` and `s3fs` to transfer files to `public_reports/PEV_Reports/Zone/{month_report}/`.
2. **Antenna Mapping**: The `zone2antenna` helper function cleans the DHIS2 names and matches the Zone to the correct Antenna.
3. **SQL Execution**:
    - Checks if the URL already exists in `public_reports`.
    - If missing: Performs a standard `INSERT`.
    - If present: Uses a temporary table (`temp_table`) to perform a bulk `UPDATE` of the `updated_at` field.
4. **File Migration**: Uses `shutil.move` to transfer files to the local archive folder. In this way, the S3 upload function only uploads the newly produced PDF files instead of re-upload every single PDF to S3 and re-trigger database updates for reports that haven't actually changed. Summary of the Folder Roles:

| **Folder**                  | **Status**   | **Role**                                                              |
| --------------------------- | ------------ | --------------------------------------------------------------------- |
| `.../out/reports/pdf/`      | **Active**   | The "Outbox." Holds new PDFs waiting for S3 upload.                   |
| `.../pdf/produced_reports/` | **Archived** | The "File Cabinet." Holds local copies of files already in the cloud. |

