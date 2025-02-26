# Vue 3 + Vite

This template should help get you started developing with Vue 3 in Vite. The template uses Vue 3 `<script setup>` SFCs, check out the [script setup docs](https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup) to learn more.
 Documentation Report

 ALL CODE THAT IS RELEVANT IS LOCATED IN THE DEPLOY BRANCH, USE DEPLOY BRANCH TO ACCESS ALL CODE!!!!!

Project Overview

This project is a Vue.js application designed to upload and parse OOXML (.docx) documents. The application extracts text content from the uploaded files and displays it in a user-friendly interface. 

 1. Parsing Methodology Employed

The parsing of OOXML files is accomplished using the following libraries:

- JSZip: Used to read the `.docx` file, which is essentially a ZIP archive containing XML files.
- xml-js: This library is employed to convert the extracted XML content into a JSON format, making it easier to navigate and extract specific elements.

Steps Involved in Parsing:

1. File Upload: The user uploads a `.docx` file via an input element.
2. File Reading: The application reads the file as an ArrayBuffer using the `FileReader` API.
3. Zip Extraction: The ArrayBuffer is passed to JSZip to load the ZIP file and extract the `word/document.xml` file, which contains the document's main content.
4. **XML to JSON Conversion**: The extracted XML is converted to JSON using the `xml-js` library.
5. **Text Extraction**: The application traverses the JSON structure to retrieve text from paragraphs, handling both arrays and single object cases.

 2. Challenges Encountered and Solutions Implemented

- File Handling: Initially, the file upload was not properly reading the contents. This was resolved by ensuring the file was read as an ArrayBuffer and correctly passed to JSZip.
- JSON Structure Complexity: The structure of the JSON generated from XML was complex, requiring detailed traversal logic. Careful examination of the generated JSON was necessary to correctly access the text nodes.
- Responsiveness: Ensuring that the extracted content displayed well on various screen sizes was addressed by applying responsive CSS styles and using flexible layout structures.

 3. Assumptions Made During Development

- It was assumed that the uploaded `.docx` files would contain well-formed XML and that the document structure would generally follow the standard OOXML specifications.
- The application was designed with the expectation that users would upload relatively simple documents without advanced formatting (e.g., tables, images) that could complicate text extraction.
- It was assumed that users would have a modern browser capable of supporting the FileReader and JSZip functionalities.

 4. Instructions for Running and Testing the Application

1. Clone the Repository:
   ```bash
   git clone https://github.com/LordofTech/vue-ooxml-parser-repo.git
   cd <repository-directory>
   ```

2. Install Dependencies:
   ```bash
   npm install
   ```

3. Run the Application:
   ```bash
   npm run dev
   ```
   Open your browser and navigate to `http://localhost:3000` (or the specified port).

4. Testing the Application:
   - Upload a `.docx` file using the file input.
   - Observe the extracted content displayed on the screen.
   - Check for any errors in the console during file upload and parsing.

5. Deployment

The application has been successfully deployed on Netlify. Here are the details:

- Deployed Application URL: [https://vue-ooxml-parser.netlify.app/](https://vue-ooxml-parser.netlify.app/)


6. Source Code Repository

The complete source code, including the application files and documentation, can be found at the following link:

- Source Code Repository: [https://github.com/LordofTech/vue-ooxml-parser-repo.git](https://github.com/LordofTech/vue-ooxml-parser-repo.git)



Learn more about IDE Support for Vue in the [Vue Docs Scaling up Guide](https://vuejs.org/guide/scaling-up/tooling.html#ide-support).
