# AppADay 070 — Flatbed

**Category:** Utility (U) · **Shipped:** 2026-07-16

**Live:** https://augustineiacopelli.github.io/appaday-070-flatbed/
**Portfolio:** https://augustineiacopelli.github.io/appaday/

A pocket flatbed scanner that lives in a browser tab. Point the camera at a page, and Flatbed finds the four corners, waits for your hands to go still, fires on its own, straightens the perspective, and holds the page in a feed tray until you send it out as a PDF.

## What it does

Point the rear camera at a document. A detector runs about eleven times a second on a downscaled frame and outlines the page in scanner-lamp cyan. Once the outline stops moving, an amber ring closes around the shutter over roughly nine tenths of a second and the capture fires without a tap. A lamp bar sweeps the frame while the page is unwarped to a flat rectangle.

Captured pages stack in the feed tray. Tap any page to change its filter, rotate it, drag its corners if the detector guessed wrong, or delete it. Export the whole tray as a letter-size PDF, save each page as a JPG, or hand the PDF straight to the share sheet, which on iOS and Android drops it into a Mail draft as a real attachment.

## Features

**Edge detection.** Each frame is downscaled to 220px wide, blurred, and split with an Otsu threshold. The largest connected component is traced to its per-row extremes, run through a monotone-chain convex hull, and reduced to the maximum-area inscribed quadrilateral. Both polarities are tried, so a white page on a dark desk and a dark page on a light desk both resolve. Candidates are rejected when they cover too little of the frame, run off the edge, fail a rectangularity test against their own hull, or have corner angles outside roughly 48 to 132 degrees. Each rejection has its own on-screen instruction: move closer, fit the whole page in frame, flatten the page, square up.

**Auto capture.** Corner drift is measured frame to frame. Under 3.5 percent of the short edge counts as still; 900ms of stillness fires the shutter. A Manual toggle turns the ring off and hands the shutter back.

**Perspective correction.** An eight-parameter homography is solved by Gaussian elimination and inverse-mapped with bilinear sampling, so the output is a flat rectangle rather than a trapezoid. Output is capped at 1500px on the long edge.

**Filters.** Original, Enhance (percentile contrast stretch so paper reads white and ink reads black), Grayscale, and B/W. B/W uses a local adaptive threshold built on an integral image, which keeps text crisp across a page lit unevenly — the case that defeats a global threshold.

**Corner adjust.** Detection is not always right. The Corners tool puts the original frame back on screen with four draggable handles.

**Export.** Letter-size PDF via jsPDF, orientation chosen per page. JPGs at quality 0.92. Email hands a real `File` to `navigator.share`; where the Web Share API cannot take files, the PDF downloads and a pre-addressed draft opens instead.

## Build notes

Single-file vanilla HTML, CSS, and JavaScript. No build step. Google Fonts (Archivo, Martian Mono) and jsPDF 2.5.1 from cdnjs are the only external dependencies. All image work is done on canvas in the browser.

Nothing is uploaded and nothing is stored. The tray is session memory only, which is the honest tradeoff for a scanner that never asks for an account — the browser warns before you navigate away with pages still in the tray. Source frames are held at 1400px so corners stay re-draggable without exhausting mobile memory.

The camera requires a secure context, which GitHub Pages provides. If access is denied, Import still accepts photos from the library and runs the same detection over them.

## Design

The subject is an office machine, so the app is built like one: a putty housing, a black platen, and a status lamp readout in mono type. The signature is the scanner lamp — a cyan bar that sweeps the capture while the page is being squared, the one moment of motion in an otherwise still interface.

## Definition of complete

- **Functional** — detection, auto capture, unwarp, filters, and all three export paths tested.
- **Single purpose** — scan a paper page into a clean digital page.
- **Mobile friendly** — built for a 375px viewport, rear camera, thumb-height controls.
- **Visually polished** — machine housing, lamp accent, Archivo and Martian Mono.
- **Published** — live on GitHub Pages.

*Ship something every day. It compounds.*
