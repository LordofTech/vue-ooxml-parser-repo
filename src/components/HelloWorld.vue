<template>
  <div class="container">
    <header class="header">
      <h1>{{ msg }}</h1>
      <p class="subheading">Upload your OOXML (.docx) or XML (.xml) document and process it with ease!</p>
    </header>

    <main class="main-content">
      <div class="counter-card">
        <button class="count-button" @click="count++">
          Count is <span class="count">{{ count }}</span>
        </button>
      </div>

      <div class="file-upload">
        <h2>Upload an OOXML (.docx) or XML (.xml) File</h2>
        <input type="file" @change="handleFileUpload" accept=".docx,.xml" class="file-input" />
        <p v-if="fileName">Uploaded File: {{ fileName }}</p>
        <button class="upload-button" @click="uploadFile">Upload</button>
      </div>

      <div v-if="extractedContent" class="content-box">
        <h3>Extracted Document Content:</h3>
        <pre class="extracted-content">{{ extractedContent }}</pre>
      </div>
    </main>

    <footer class="footer">
      <p>
        Check out <a href="https://vuejs.org/guide/quick-start.html#local" target="_blank">create-vue</a>, the official Vue + Vite starter.
      </p>
      <p>
        Learn more about IDE Support for Vue in the
        <a href="https://vuejs.org/guide/scaling-up/tooling.html#ide-support" target="_blank">Vue Docs Scaling up Guide</a>.
      </p>
      <p class="read-the-docs">Click on the Vite and Vue logos to learn more</p>
    </footer>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import JSZip from 'jszip';
import xmlJs from 'xml-js';

// Props
defineProps({
  msg: String,
});

// Reactive state
const count = ref(0);
const fileName = ref('');
const extractedContent = ref('');

// Emit event for file upload
const emit = defineEmits(['file-uploaded']);

// Handle file change
const handleFileUpload = (event) => {
  const file = event.target.files[0];
  if (file) {
    fileName.value = file.name;
    const fileType = file.name.split('.').pop().toLowerCase();

    if (fileType === 'docx') {
      readDocxFile(file);
    } else if (fileType === 'xml') {
      readXmlFile(file);
    } else {
      extractedContent.value = "Unsupported file format. Please upload a .docx or .xml file.";
    }
  }
};

// Read the uploaded OOXML (.docx) file
const readDocxFile = async (file) => {
  const reader = new FileReader();
  reader.onload = async (event) => {
    const arrayBuffer = event.target.result;

    try {
      const zip = await JSZip.loadAsync(arrayBuffer);
      const xmlData = await zip.file("word/document.xml").async("text");
      const jsonData = JSON.parse(xmlJs.xml2json(xmlData, { compact: true, spaces: 2 }));

      console.log("Parsed JSON data:", jsonData); // Debugging output

      const body = jsonData["w:document"]?.["w:body"];
      const paragraphs = body?.["w:p"] || [];

      if (Array.isArray(paragraphs) && paragraphs.length > 0) {
        extractedContent.value = paragraphs.map(p => extractText(p)).join("\n");
      } else {
        extractedContent.value = "No readable text found in the document.";
      }

    } catch (error) {
      console.error("Error processing .docx document:", error);
      extractedContent.value = "Error reading .docx document: " + error.message;
    }
  };
  reader.readAsArrayBuffer(file);
};

// Read the uploaded XML (.xml) file
const readXmlFile = (file) => {
  const reader = new FileReader();
  reader.onload = (event) => {
    try {
      const xmlText = event.target.result;
      extractedContent.value = xmlText; // Display raw XML content

      // Attempt to parse into JSON for better readability
      const jsonData = JSON.parse(xmlJs.xml2json(xmlText, { compact: true, spaces: 2 }));
      console.log("Parsed XML JSON:", jsonData);
    } catch (error) {
      console.error("Error processing XML file:", error);
      extractedContent.value = "Error reading XML document: " + error.message;
    }
  };
  reader.readAsText(file);
};

const extractText = (paragraph) => {
  console.log("Extracting from paragraph:", paragraph);
  if (!paragraph["w:r"]) return "";

  if (Array.isArray(paragraph["w:r"])) {
    return paragraph["w:r"].map(run => run["w:t"]?._text || "").join(" ");
  } else if (typeof paragraph["w:r"] === 'object') {
    return paragraph["w:r"]["w:t"]?._text || "";
  }

  return "";
};

// Placeholder for upload logic
const uploadFile = () => {
  console.log('File uploaded:', fileName.value);
};
</script>

<style scoped>
.container {
  font-family: 'Arial', sans-serif;
  max-width: 800px;
  margin: auto;
  padding: 20px;
  background: linear-gradient(135deg, rgba(173, 216, 230, 1) 0%, rgba(50, 50, 50, 0.5) 100%);
  border-radius: 8px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  color: #333;
}

.header {
  text-align: center;
  margin-bottom: 20px;
}

.subheading {
  color: #666;
}

.main-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
}

.counter-card {
  background-color: #ffffff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  text-align: center;
}

.count-button {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.count-button:hover {
  background-color: #0056b3;
}

.count {
  font-weight: bold;
  font-size: 1.5em;
}

.file-upload {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
}

.file-input {
  border: 1px solid #ccc;
  border-radius: 5px;
  padding: 10px;
  width: 100%;
}

.upload-button {
  background-color: #28a745;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.upload-button:hover {
  background-color: #218838;
}

.content-box {
  background: #f8f8f8;
  padding: 15px;
  border-radius: 5px;
  margin-top: 20px;
  text-align: left;
  white-space: pre-wrap;
  overflow-x: auto;
  width: 100%;
}

.footer {
  margin-top: 30px;
  text-align: center;
  font-size: 14px;
  color: #666;
}
</style>
