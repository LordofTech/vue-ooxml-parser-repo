<template>
  <div class="upload-container">
    <h2>Upload an OOXML (.docx) File</h2>
    <input type="file" @change="handleFileUpload" accept=".docx" />
    <p v-if="fileName">Uploaded File: {{ fileName }}</p>
    <div v-if="extractedContent" class="content-box">
      <h3>Extracted Document Content:</h3>
      <pre>{{ extractedContent }}</pre>
    </div>
  </div>
</template>

<script>
import JSZip from 'jszip';
import xmlJs from 'xml-js';

export default {
  data() {
    return {
      fileName: '',
      extractedContent: '',
    };
  },
  methods: {
    handleFileUpload(event) {
      const file = event.target.files[0];
      if (file) {
        this.fileName = file.name;
        this.readFile(file);
      }
    },
    
    async readFile(file) {
      const reader = new FileReader();
      reader.onload = async (event) => {
        const arrayBuffer = event.target.result;

        try {
          // Load the document
          const zip = await JSZip.loadAsync(arrayBuffer);
          const xmlData = await zip.file("word/document.xml").async("text");

          // Convert XML to JSON
          const jsonData = JSON.parse(xmlJs.xml2json(xmlData, { compact: true, spaces: 2 }));

          // Extract paragraphs
          const paragraphs = jsonData["w:document"]["w:body"]["w:p"] || [];

          // Process text content
          this.extractedContent = paragraphs.map(p => this.extractText(p)).join("\n");

        } catch (error) {
          console.error("Error processing document:", error);
          this.extractedContent = "Error reading document.";
        }
      };
      reader.readAsArrayBuffer(file);
    },

    extractText(paragraph) {
      if (!paragraph["w:r"]) return "";
      return paragraph["w:r"].map(run => run["w:t"]?._text || "").join(" ");
    }
  }
};
</script>

<style scoped>
.upload-container {
  text-align: center;
  margin: 20px;
}

.content-box {
  background: #f8f8f8;
  padding: 15px;
  border-radius: 5px;
  margin-top: 20px;
  text-align: left;
  white-space: pre-wrap;
}
</style>
