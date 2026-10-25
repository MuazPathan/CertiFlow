# CertiFlow — Certificate Generator & Bulk Mailer

A browser-based tool for generating personalised certificates from a template image and an Excel spreadsheet, with built-in bulk email delivery via SMTP.

> Built by [Muaz Pathan](https://www.linkedin.com/in/muaz-pathan)

---

## Features

- **Drag-and-drop certificate canvas** — upload any PNG/JPG/WebP as your certificate background and visually position text fields on it
- **Excel / CSV import** — every column becomes a draggable field; supports `.xlsx`, `.xls`, and `.csv`
- **Rich typography** — 20+ fonts (handwriting, serif, sans-serif), size, color, bold/italic, alignment, text transform, and drop shadow
- **Per-row overrides** — click any row in the data table to preview and fine-tune font/position for that specific recipient without affecting others
- **Bulk download** — export all certificates as a ZIP in one click, with a live progress bar
- **SMTP email delivery** — send each certificate as a PNG attachment directly from the browser via your own SMTP server
- **Email composer** — rich-text editor with `{{variable}}` placeholders, link insertion, and reusable templates saved in `localStorage`
- **Zoom controls** — 25 % → 100 % canvas zoom with auto-fit on load
- **Touch support** — drag fields on mobile/tablet

---

## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) v16 or later

### Installation

```bash
git clone https://github.com/MuazPathan/CertiFlow.git
cd WiMailer
npm install
```

### Run

```bash
npm start
```

Then open **http://localhost:3001** in your browser.

---

## Usage

### 1. Load a Certificate Template

Drag and drop (or click to browse) a PNG, JPG, or WebP image onto the canvas drop zone. This is your blank certificate background.

### 2. Import Recipient Data

In the **Excel Import** sidebar panel, upload an `.xlsx`, `.xls`, or `.csv` file. Each column header becomes a draggable field chip. The preview table shows the first few rows.

**Minimum expected columns:**

| Column | Purpose |
|--------|---------|
| `name` (or any column containing "name") | Recipient's full name |
| `email` (or any column containing "email") | Delivery address for SMTP send |

Any additional columns (e.g. `course`, `date`, `grade`) are also available as fields and email variables.

### 3. Place Fields on the Canvas

Drag a column chip from the **Column Fields** panel onto the certificate canvas. The field snaps to wherever you drop it. Click a placed field to select it, then drag it to reposition.

### 4. Style Each Field

With a field selected, use the **Typography** panel to set:

- Font family (categorised: Handwriting, Elegant Serif, Classic, Sans-serif, Monospace)
- Size, colour (colour picker + hex input)
- Bold / Italic
- Alignment: left / centre / right
- Text transform: none / UPPERCASE / Title Case
- Drop shadow: colour, blur, X/Y offset, opacity

### 5. Per-Row Customisation (optional)

Click any row in the data preview table to enter **row-edit mode**. While in this mode, any typography or position change is saved as an override for that row only — useful for names that need a smaller font or a slightly different position. Rows with overrides show a ✦ indicator. Click **Exit row edit** or **Reset row** to return to global defaults.

### 6. Download

| Button | Output |
|--------|--------|
| **Download Preview (1st row)** | Single PNG for the currently previewed row |
| **Download All as ZIP** | All certificates packed into `certificates.zip` |

### 7. Send by Email (SMTP)

#### Configure SMTP

Click **Setup SMTP** and fill in:

| Field | Example |
|-------|---------|
| SMTP Host | `smtp.gmail.com` |
| Port | `587` (TLS) or `465` (SSL) |
| Username | `you@example.com` |
| Password / App password | `•••••••` |
| From Name | `Certificate Team` |

Click **Test Connection** to verify credentials before saving.

> **Gmail users**: generate an [App Password](https://myaccount.google.com/apppasswords) and use that instead of your account password.

#### Compose the Email

Click **Compose Email** to open the rich-text editor. Use `{{variable}}` placeholders that map to your Excel column names:

```
Subject: Congratulations, {{firstName}}!

Body:
Hi {{firstName}},

Please find your certificate for {{course}} attached.

Best regards,
The Team
```

`{{firstName}}` is automatically derived from the `name` column (first word). All other `{{columnName}}` variables are replaced per recipient at send time.

Save frequently used templates via **Save Template**, then reload them from the **Load Template** dropdown.

#### Send

Click **Send Certificates by Email**. The tool:

1. Generates each certificate PNG on a hidden canvas
2. Applies per-row overrides if any
3. Substitutes `{{variables}}` in subject and body
4. Sends the email with the PNG as an attachment via your SMTP server
5. Shows a live log of successes and failures

Rows without an email address are skipped with a warning.

---

## Project Structure

```
WiMailer/
├── index.html        # Single-page UI
├── server.js         # Express server — SMTP proxy (POST /api/send-email, POST /api/test-smtp)
├── js/
│   └── app.js        # All client-side logic
├── css/
│   └── style.css     # Dark-theme stylesheet
└── package.json
```

---

## API Reference (server.js)

### `POST /api/test-smtp`

Verifies SMTP credentials without sending a message.

**Body**
```json
{
  "smtp": {
    "host": "smtp.gmail.com",
    "port": 587,
    "user": "you@example.com",
    "pass": "app-password"
  }
}
```

**Response** `200 { "ok": true }` or `500 { "error": "..." }`

---

### `POST /api/send-email`

Sends one email with a certificate PNG attachment.

**Body**
```json
{
  "smtp": { "host": "...", "port": 587, "user": "...", "pass": "...", "fromName": "..." },
  "to": "recipient@example.com",
  "subject": "Your Certificate",
  "html": "<p>Hi Alice,</p><p>Please find your certificate attached.</p>",
  "attachmentBase64": "<base64-encoded PNG>",
  "filename": "Alice_certificate.png"
}
```

**Response** `200 { "ok": true }` or `500 { "error": "..." }`

---

## Use Cases

### Graduation / Completion Certificates
Prepare a designed certificate image in Canva or Photoshop, export as PNG, import your student roster (name, email, course), place the name field, and send to hundreds of students in one click.

### Workshop & Event Attendance
After an event, drop in your attendance sheet, position participant names on the certificate canvas, and email everyone their personalised certificate within minutes.

### Competition / Award Certificates
Use per-row overrides to adjust font size for participants with unusually long names, keeping every certificate visually consistent without manual editing.

### Internal HR Recognition
Send personalised "Employee of the Month" or onboarding welcome certificates using your company SMTP relay.

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Vanilla JS, HTML5 Canvas, CSS custom properties |
| Excel parsing | [SheetJS (xlsx)](https://sheetjs.com/) via CDN |
| ZIP generation | [JSZip](https://stuk.github.io/jszip/) via CDN |
| Backend / SMTP proxy | [Node.js](https://nodejs.org/) + [Express](https://expressjs.com/) + [Nodemailer](https://nodemailer.com/) |

---

## License

MIT

---

## Author

**Muaz Pathan** — [LinkedIn](https://www.linkedin.com/in/muaz-pathan/)
