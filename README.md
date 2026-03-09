<!DOCTYPE html>
<html>
<head>
<title>GST 2B Reconciliation Tool</title>

```
<!-- SheetJS Library -->
<script src="<https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js>"></script>

<style>
    body {
        font-family: Arial, sans-serif;
        background: linear-gradient(to right, #1e3c72, #2a5298);
        margin: 0;
        padding: 0;
    }

    .container {
        width: 55%;
        margin: 60px auto 20px auto;
        background: white;
        padding: 30px;
        border-radius: 10px;
    }

    h2 {
        margin-top: 0;
    }

    button {
        width: 100%;
        padding: 12px;
        background-color: #2a5298;
        color: white;
        border: none;
        font-size: 15px;
        margin-top: 10px;
        cursor: pointer;
        border-radius: 6px;
    }

    button:hover {
        background-color: #1e3c72;
    }

    .small-btn {
        background-color: #4CAF50;
        font-size: 13px;
        padding: 8px;
    }

    .small-btn:hover {
        background-color: #3e8e41;
    }

    .result {
        margin-top: 20px;
        background: #f4f4f4;
        padding: 15px;
        border-radius: 6px;
    }

    .footer-credit {
        text-align: center;
        font-size: 13px;
        color: rgba(255,255,255,0.85);
        margin-bottom: 20px;
    }

    .info-box {
        background:#e7f3ff;
        padding:15px;
        border-radius:8px;
        margin-bottom:20px;
    }
</style>
```

</head>

<body>

<div class="container">
<h2>GST 2B vs Purchase Register Reconciliation</h2>

```
<div class="info-box">
    <b>Important:</b>
    This tool works only with the official template.
    Download the template, paste your data in the same structure, and upload.
</div>

<label><b>Upload GSTR 2B File:</b></label>
<input type="file" id="file2b" accept=".xls,.xlsx">
<button class="small-btn" onclick="downloadTemplate()">Download GSTR 2B Template</button>

<br><br>

<label><b>Upload Purchase Register:</b></label>
<input type="file" id="fileBooks" accept=".xls,.xlsx">
<button class="small-btn" onclick="downloadTemplate()">Download Purchase Register Template</button>

<button onclick="reconcile()">Reconcile & Generate Report</button>
<button onclick="downloadReport()">Download Excel Report</button>

<div class="result" id="result"></div>
```

</div>

<div class="footer-credit">
Professionally Developed by Sidhesh Jha
</div>

<script>

let missingIn2B = [];
let missingInBooks = [];

const requiredHeaders = [
"GSTIN of supplier",
"Trade/Legal name",
"Invoice number",
"Invoice Date",
"Invoice Value(₹)",
"Taxable Value (₹)",
"IGST",
"CGST",
"SGST"
];

function downloadTemplate() {
const ws = XLSX.utils.aoa_to_sheet([requiredHeaders]);
const wb = XLSX.utils.book_new();
XLSX.utils.book_append_sheet(wb, ws, "Template");

```
const wbout = XLSX.write(wb, { bookType: "xlsx", type: "array" });
const blob = new Blob([wbout], { type: "application/octet-stream" });
const link = document.createElement("a");
link.href = URL.createObjectURL(blob);
link.download = "GST_Reconciliation_Template.xlsx";
document.body.appendChild(link);
link.click();
document.body.removeChild(link);
```

}

function readExcel(file, callback) {
const reader = new FileReader();
reader.onload = function(e) {
const data = new Uint8Array(e.target.result);
const workbook = XLSX.read(data, { type: 'array' });
const sheet = workbook.Sheets[workbook.SheetNames[0]];
const jsonData = XLSX.utils.sheet_to_json(sheet, { defval: "" });
callback(jsonData);
};
reader.readAsArrayBuffer(file);
}

function validateHeaders(data) {
const headers = Object.keys(data[0] || {});
return requiredHeaders.every(h => headers.includes(h));
}

function normalizeInvoice(inv) {
return String(inv).trim().toUpperCase();
}

function toNumber(val) {
return Number(val) || 0;
}

function reconcile() {

```
const file2b = document.getElementById("file2b").files[0];
const fileBooks = document.getElementById("fileBooks").files[0];

if (!file2b || !fileBooks) {
    alert("Please upload both files.");
    return;
}

readExcel(file2b, function(data2b) {
    readExcel(fileBooks, function(dataBooks) {

        if (!validateHeaders(data2b) || !validateHeaders(dataBooks)) {
            alert("Invalid template format. Please use the official template.");
            return;
        }

        const map2b = new Map();
        const mapBooks = new Map();

        data2b.forEach(row => {
            const key = normalizeInvoice(row["Invoice number"]);
            if (key) map2b.set(key, row);
        });

        dataBooks.forEach(row => {
            const key = normalizeInvoice(row["Invoice number"]);
            if (key) mapBooks.set(key, row);
        });

        missingInBooks = [];
        missingIn2B = [];

        map2b.forEach((value, key) => {
            if (!mapBooks.has(key)) {
                missingInBooks.push(formatRow(value));
            }
        });

        mapBooks.forEach((value, key) => {
            if (!map2b.has(key)) {
                missingIn2B.push(formatRow(value));
            }
        });

        document.getElementById("result").innerHTML =
            "<b>Reconciliation Complete</b><br><br>" +
            "Total 2B Invoices: " + map2b.size + "<br>" +
            "Total Book Invoices: " + mapBooks.size + "<br><br>" +
            "<b>Missing in Books:</b> " + missingInBooks.length + "<br>" +
            "<b>Missing in 2B:</b> " + missingIn2B.length;

    });
});
```

}

function formatRow(row) {
const igst = toNumber(row["IGST"]);
const cgst = toNumber(row["CGST"]);
const sgst = toNumber(row["SGST"]);
const totalTax = igst + cgst + sgst;

```
return {
    "GSTIN of supplier": row["GSTIN of supplier"],
    "Trade/Legal name": row["Trade/Legal name"],
    "Invoice number": row["Invoice number"],
    "Invoice Date": row["Invoice Date"],
    "Invoice Value(₹)": toNumber(row["Invoice Value(₹)"]),
    "Taxable Value (₹)": toNumber(row["Taxable Value (₹)"]),
    "IGST": igst,
    "CGST": cgst,
    "SGST": sgst,
    "Total Tax": totalTax
};
```

}

function downloadReport() {

```
if (missingIn2B.length === 0 && missingInBooks.length === 0) {
    alert("Please run reconciliation first.");
    return;
}

const wb = XLSX.utils.book_new();

const headers = [
    "GSTIN of supplier",
    "Trade/Legal name",
    "Invoice number",
    "Invoice Date",
    "Invoice Value(₹)",
    "Taxable Value (₹)",
    "IGST",
    "CGST",
    "SGST",
    "Total Tax"
];

const ws1 = XLSX.utils.json_to_sheet(missingInBooks, { header: headers });
const ws2 = XLSX.utils.json_to_sheet(missingIn2B, { header: headers });

XLSX.utils.book_append_sheet(wb, ws1, "Missing in Books");
XLSX.utils.book_append_sheet(wb, ws2, "Missing in 2B");

const wbout = XLSX.write(wb, { bookType: "xlsx", type: "array" });
const blob = new Blob([wbout], { type: "application/octet-stream" });

const link = document.createElement("a");
link.href = URL.createObjectURL(blob);
link.download = "Reconciliation_Report.xlsx";
document.body.appendChild(link);
link.click();
document.body.removeChild(link);

alert("Report Downloaded Successfully!");
```

}

</script>

</body>
</html>
