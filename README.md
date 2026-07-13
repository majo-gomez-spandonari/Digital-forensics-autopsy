# Digital Forensics — File Recovery with Autopsy

## Objective
Simulate a realistic file-deletion scenario on a USB drive, then use
Autopsy (with the PhotoRec Carver module) to recover permanently deleted
files and validate the integrity of the recovered content.

## Environment
- Tool: Autopsy 4.23.1 (Windows)
- Evidence source: USB drive (Local Disk, direct analysis — no forensic
  image required for this exercise)
- Case type: Single-User

## What I did

**1. Built a realistic deletion scenario**
Before any analysis, populated a USB drive with varied working documents
(PDFs, brochures, images) to have real evidence to recover. Verified and
documented the drive's initial contents before deletion — a baseline is
essential in forensics; without it, you can't prove what was actually
recovered versus what was already there.

**2. Permanent deletion (not Recycle Bin)**
Deleted a representative selection of files using **Shift+Delete**, which
bypasses the Recycle Bin entirely. This matters for the exercise to be
forensically realistic: Shift+Delete only marks the file's disk space as
unallocated — the data itself remains physically present until
overwritten, which is exactly the condition that makes carving-based
recovery (PhotoRec) possible.

**3. Case creation**
Created a new Autopsy case (`USB_Forense`, Single-User type), recording
examiner details and a case description — standard chain-of-custody
practice even in a lab exercise.

**4. Added the USB as a data source**
Selected **Local Disk** as the source type (direct physical device
analysis). Enabled ingest modules: **PhotoRec Carver** (the key module for
recovering deleted/unallocated content), plus File Type Identification,
Keyword Search, Email Parser, Interesting Files Identifier, and Data
Source Integrity.

**Troubleshooting note:** on the first attempt, I added the USB as a data
source *before* actually deleting the test files — Autopsy captures the
device state at the exact moment of acquisition, so scanning a drive with
nothing deleted yet means no unassigned space exists to recover from.
Cancelled and re-ran the data source wizard after completing the deletion
step. (Sequencing matters in forensic acquisition — this is a real
lesson, not just a lab mistake.)

**5. Processing and results**
During processing, Autopsy explicitly reported `Adding: $OrphanFiles` —
an early signal of orphaned/dereferenced file system entries consistent
with recently deleted files.

Final results in the **Deleted Files** category: **14 files recovered**,
via two distinct mechanisms:
- **File system recovery (10 files):** directory entries still existed,
  so original filenames were recoverable along with content
  (e.g. `manguito rotador.jpg`)
- **Carving recovery via PhotoRec (remainder):** files reconstructed from
  binary signatures in unallocated space, identifiable by the `f0000000...`
  prefix — original filenames are lost with this method, but full content
  integrity is preserved

**6. Validating recovery integrity**
Switched to Thumbnail view to visually confirm that both the
file-system-recovered copy and the carved copy (different filenames)
were in fact the *same* underlying image — confirming the recovery
was accurate, not just superficially plausible.

**7. Evidence export**
Exported recovered evidence (one image, one full multi-page PDF) via
Autopsy's **Extract File(s)** function, confirming the exported PDF
retained all 36 original pages intact.

## Key result
Successfully recovered 14 permanently deleted files from a USB drive
using two complementary recovery mechanisms — file-system metadata
recovery and PhotoRec carving — and validated content integrity across
both methods. Confirms the core forensic principle: OS-level deletion
frees allocation space but does not destroy data until it's overwritten.

## Files in this repo
- `screenshots/` — case creation, data source setup, ingest processing,
  recovered file listing, thumbnail validation, evidence export
