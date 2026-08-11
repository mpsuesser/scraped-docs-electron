---
url: https://www.electronjs.org/docs/latest/api/structures/print-to-pdf-options
title: "Print To Pdf Options"
description: ""
access_date: 2026-08-11T19:40:43.075Z
current_date: 2026-08-11T19:40:43.075Z
---

- `landscape` boolean (optional) - Paper orientation.`true` for landscape, `false` for portrait. Defaults to false.
- `displayHeaderFooter` boolean (optional) - Whether to display header and footer. Defaults to false.
- `printBackground` boolean (optional) - Whether to print background graphics. Defaults to false.
- `scale` number(optional) - Scale of the webpage rendering. Defaults to 1.
- `pageSize` string | Size (optional) - Specify page size of the generated PDF. Can be `A0`, `A1`, `A2`, `A3`, `A4`, `A5`, `A6`, `Legal`, `Letter`, `Tabloid`, `Ledger`, or an Object containing `height` and `width` in inches. Defaults to `Letter`.
- `margins` [PrintToPDFMargins](print-to-pdf-margins.md) (optional)
	- `top` number (optional) - Top margin in inches. Defaults to 1cm (~0.4 inches).
		- `bottom` number (optional) - Bottom margin in inches. Defaults to 1cm (~0.4 inches).
		- `left` number (optional) - Left margin in inches. Defaults to 1cm (~0.4 inches).
		- `right` number (optional) - Right margin in inches. Defaults to 1cm (~0.4 inches).
- `pageRanges` string (optional) - Page ranges to print, e.g., '1-5, 8, 11-13'. Defaults to the empty string, which means print all pages.
- `headerTemplate` string (optional) - HTML template for the print header. Should be valid HTML markup with following classes used to inject printing values into them: `date` (formatted print date), `title` (document title), `url` (document location), `pageNumber` (current page number) and `totalPages` (total pages in the document). For example, `<span class=title></span>` would generate span containing the title.
- `footerTemplate` string (optional) - HTML template for the print footer. Should use the same format as the `headerTemplate`.
- `preferCSSPageSize` boolean (optional) - Whether or not to prefer page size as defined by css. Defaults to false, in which case the content will be scaled to fit the paper size.
- `generateTaggedPDF` boolean (optional) *Experimental* - Whether or not to generate a tagged (accessible) PDF. Defaults to false. As this property is experimental, the generated PDF may not adhere fully to PDF/UA and WCAG standards.
- `generateDocumentOutline` boolean (optional) *Experimental* - Whether or not to generate a PDF document outline from content headers. Defaults to false.
