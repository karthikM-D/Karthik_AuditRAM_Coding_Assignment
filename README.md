**Text Search \& Bounding Box Overlay Tool**

This project provides a Python-based tool that allows users to upload a document, search for specific text inside it, and generate an output image/PDF with bounding boxes (red, unfilled rectangles) around all matched text — without modifying the original file.



Bounding boxes like these are often used in auditing and compliance workflows to visually highlight relevant evidence, making it easier for auditors to validate the presence of required phrases or indicators.





**Table of Contents:**

1. Project Overview

2\. Supported File Types

3\. Features

4\. System Requirements

5\. Installation Guide (Windows / macOS / Linux)

6\. How the Tool Works (Technical Approach)

7\. How to Run the Program

8\. Project Structure





**1. Project Overview:**

This tool accepts two inputs:



A file upload (PDF, Word, Excel, or Image)



A text string to search for



The program scans the entire document, finds every occurrence of the input text, and produces an output file with a red, unfilled bounding box overlay around the found text.



Note: The original file is never modified — the bounding boxes are drawn on a generated image/PDF layer.





**2. Supported File Types:**

File Type:              Method

PDF                     pdfplumber text extraction + coordinate mapping

Image (PNG, JPG, JPEG)	OCR with Tesseract + bounding boxes

Excel (.xlsx)	        openpyxl + sheet-to-image conversion

Word (.docx)	        python-docx + DOCX → image/PDF conversion





**3. Features:** Some of the features of this program are full-document text search, bounding box overlay (red, unfilled), output PDF or PNG, Smart text matching (case-insensitive), multiformat support, doesn’t change original document and multi-platform (Windows, macOS, Linux).





**4. System Requirements:**
Python 3.8 or higher

pip (Python package manager)

Tesseract OCR (for image files)

LibreOffice (for DOCX conversion)—optional



**5. Installation Guide:**

Below are OS‑specific instructions so anyone can run this tool.



**A. Windows Installation**

i. Install Python

Download here: https://www.python.org/downloads/windows/

Note: Check “Add Python to PATH” while installing.



ii. Install Dependencies

Using requirements.txt:
Run this code: "pip install -r requirements.txt"



Or manually install each dependency:

Run this Code: "pip install pdfplumber pillow pytesseract openpyxl python-docx excel2img docx2pdf"



iii. Install Tesseract OCR (Windows)

Download the installer: https://github.com/UB-Mannheim/tesseract/wiki



Note: After installation, verify:

Run: "tesseract --version"



Note: After installation, ensure this path is in your PATH:

C:\\Program Files\\Tesseract-OCR\\ (or wherever it is installed)



**B. macOS Installation**



i. Install Python (if needed)
Run: "brew install python"



ii. Install dependencies

Run: "pip3 install -r requirements.txt"



iii. Install Tesseract

Run: "brew install tesseract"



Note: After installation, verify:

Run: "tesseract --version"



**C. Linux Installation**



i. Install Python

Run: "sudo apt update

      sudo apt install python3 python3-pip"



ii. Install dependencies

Run: "pip install -r requirements.txt"



iii. Install Tesseract

Run:"sudo apt install tesseract-ocr"

Note: After installation, verify:

Run: "tesseract --version"





**6. How the Tool Works (Technical Approach)**

**A. File Reading:**

When the user selects a file, the program first checks its extension to understand what type of document it is.

Based on the extension, the program sends the file to the correct processing function:



i) .pdf → handled by the PDF processor

ii) .png, .jpg, .jpeg → handled by the image processor

iii) .xlsx → handled by the Excel processor

iv) .docx → handled by the Word processor



Each processor is responsible for extracting text and finding where it appears in the document.



**B. Text Extraction and Position:**

Different types of files store text in different ways.

To make bounding boxes, the program does two things:



a) The actual text inside the file

b) The location of each word (its coordinates)



Each file format follows a different method:

**i) PDF Files**

PDFs store text with precise coordinates.

The program uses pdfplumber to:

a. Open the PDF page by page

b. Extract each word along with its coordinates

c. Store the coordinates (x0, top, x1, bottom) for later drawing. This provides accurate bounding boxes.



**ii) Image Files (PNG, JPG, JPEG)**

Images do not contain searchable text — only pixels.

To detect words, the program uses OCR with Tesseract, which reads the image and returns:

a. Recognized words

b.Their positions (left, top, width, height). This essentially converts the image into a searchable text map.



**iii) Excel Files (.xlsx)**

Excel files store text inside cells. Cells do not have pixel coordinates, so two steps are needed:

a. Read cell content using openpyxl

b. Convert the entire sheet into an image using excel2img. After conversion, the program calculates where each cell appears on the generated image and uses those positions to draw bounding boxes.



**iv) Word Documents (.docx)**

Word documents do not store fixed text positions.

To work around this, the program converts the DOCX into a format that does have coordinates:

a. Convert DOCX → PDF (using docx2pdf)

b. Process the PDF just like any other PDF

c. Extract text + coordinates using pdfplumber. This gives consistent positions for bounding boxes.



**C. User’s Text Search**

Once the program has extracted all text and positions, it searches for the user’s input string.

The search is case-insensitive

Partial matches are supported

Each match stores:

a. The matched word

b. The page (if PDF)

c. The bounding box coordinates

Example:

{

  "text": "audit",

  "page": 1,

  "x": 120.5,

  "y": 240.0,

  "width": 50,

  "height": 18

}

These results are then passed to the drawing step.



**D. Drawing the Bounding Boxes**

The program uses Pillow (PIL) to draw visible overlays.

Steps:

a. Convert the document (PDF page or Excel sheet) to an image

b. For each match, draw a red, unfilled rectangle:

draw.rectangle(

    (x, y, x + width, y + height),

    outline="red",

    width=3

)

c. Save the modified copy into the output/ folder. The original document is not changed — bounding boxes are drawn only on the exported image/PDF.



**E. Generating the Final Output**

Depending on the input format, the output may be:

i. A single annotated PNG

ii. Multiple annotated PNGs merged into a PDF

iii. An annotated PDF created from PDF pages. All output files are saved inside the project’s output/ directory.



**F. Error Handling**

The program includes basic checks to ensure smooth execution:

a. Unsupported file → user gets a clear message

b. Empty search string → not allowed

c. No matches found → program still generates an output (with no boxes)

d. Missing OCR engine → program informs user to install Tesseract



**G. Running the Program**

After the program has processed the file and prepared the text‑matching logic, the final step is for the user to run the script and generate the output with bounding boxes. The program is designed to run directly from the terminal, and the workflow is the same for any supported file type.



i. Open a terminal or command prompt

Navigate to your project directory:

cd "your\_project\_folder"


ii. Run the main script

"python app.py"


iii. Follow the prompts

The program will ask you to:
a. Enter the path to the input file: This can be a PDF, Word document, Excel file, or an image.

b. Enter the text you want to search for: The search is case‑insensitive.

c. Wait for processing: The program extracts text, finds matches, and draws bounding boxes.



iv. Find your output

All generated output files are saved in the "output/" folder.

Depending on the file type, you will receive:

a. Annotated PNG files (per page or sheet).

b. A combined annotated PDF.

c. Or an annotated image if the input was an image file.

Note: The original input file is never modified — the program always works on a generated copy.





**7. How to Run the Program:**

Run this in your terminal:

python app.py

You will be prompted to:

Upload/enter path of a file

Enter the text you want to search

Wait for processing

Find the results inside the /output folder





**8. Project Structure**

project/

│── app.py

│── requirements.txt

│── README.md

│── utils/

│     ├── pdf\_handler.py

│     ├── image\_handler.py

│     ├── excel\_handler.py

│     ├── docx\_handler.py

│── output/

│── samples/

