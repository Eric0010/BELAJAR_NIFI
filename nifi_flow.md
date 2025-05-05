# NiFi File Ingestion Flow

This document describes how to configure an Apache NiFi flow to **GetFile** from a local directory and **PutFile** to another directory. The flow reads `cities.csv` from the input directory and writes it to the output directory.

---

## Prerequisites

- Apache NiFi 2.3.0 installed and running.
- The NiFi UI accessible (e.g., http://localhost:8080/nifi).
- Directories on the NiFi host:
  - `/home/nifi/nifi-2.3.0/input_files/`
  - `/home/nifi/nifi-2.3.0/output_files/`

---

## 1. Download Sample Data

On the NiFi host, run:

```bash
cd /home/nifi/nifi-2.3.0/input_files/
wget https://people.sc.fsu.edu/~jburkardt/data/csv/cities.csv
```

This places `cities.csv` in the input directory.

---

## 2. Configure GetFile Processor

1. Drag a **GetFile** processor onto the canvas.
2. Open **Configure** and set:
   - **Input Directory**: `/home/nifi/nifi-2.3.0/input_files/`
   - **File Filter**: `[^\.].*`
   - **Batch Size**: `10`
   - **Keep Source File**: `true`
   - **Recurse Subdirectories**: `true`
   - **Polling Interval**: `0 sec`
   - **Ignore Hidden Files**: `true`

3. Apply and start the processor.

---

## 3. Configure PutFile Processor

1. Drag a **PutFile** processor onto the canvas.
2. Open **Configure** and set:
   - **Directory**: `/home/nifi/nifi-2.3.0/output_files`
   - **Conflict Resolution Strategy**: `replace`
   - **Create Missing Directories**: `true`

3. Apply and start the processor.

---

## 4. Connect Processors

1. Draw a connection from **GetFile** to **PutFile**.
2. Use the default relationship **success**.

---

## 5. Verify Flow

- Place `cities.csv` in the input directory.
- Observe the file appear in the output directory:
  ```bash
  ls /home/nifi/nifi-2.3.0/output_files
  ```

---

## Troubleshooting

- **No FlowFiles Queued**: Ensure permissions allow NiFi to read/write directories.
- **File Not Transferred**: Check processor logs for errors.
- **Duplicate Files**: Adjust **Keep Source File** or add a **DeleteFile** processor downstream.

---

_End of document._
