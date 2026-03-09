# GST-Rec
Browser-based GST reconciliation tool that compares GSTR-2B and Purchase Register Excel files to identify invoice mismatches. Instantly finds invoices missing in books or 2B and generates a downloadable Excel reconciliation report. No data upload — everything runs locally in the browser
# GST 2B Reconciliation Tool

A simple web-based tool to reconcile **GSTR-2B/2A data with Purchase Register data** using Excel files.

This tool helps accountants and GST professionals quickly identify mismatched invoices between 2B/2A and books.

No installation required. Runs directly in the browser.

---

## Features

• Upload GSTR-2B Excel file  
• Upload Purchase Register Excel file  
• Automatic invoice comparison  
• Detect invoices:
  - Missing in Books
  - Missing in 2B
• Generate downloadable Excel reconciliation report
• Data stays on your computer (no server upload)

---

## How to Use

1. Download the template provided in the tool.
2. Paste your data in the template format.
3. Upload:

   - GSTR-2B File
   - Purchase Register File

4. Click **Reconcile & Generate Report**.
5. View reconciliation summary.
6. Click **Download Excel Report** to get the results.

---

## Template Format

The Excel file must contain these columns:

GSTIN of supplier  
Trade/Legal name  
Invoice number  
Invoice Date  
Invoice Value(₹)  
Taxable Value (₹)  
IGST  
CGST  
SGST

The tool will not work if the format is different.

---

## Technology Used

• HTML  
• CSS  
• JavaScript  
• SheetJS (XLSX library)

---

## Privacy

All processing happens in the browser.  
No data is uploaded or stored online.

---

## Author

Developed by **Sidhesh Jha**
