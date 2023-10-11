# Image_Recognition_with_IBM_Cloud_Visual_Recognition





<!DOCTYPE html>
<html>
<head>
    <title>Image Recognition</title>
</head>
<body>
    <h1>Image Recognition System</h1>
    <input type="file" id="imageInput">
    <button id="uploadButton">Upload Image</button>
    <div id="results"></div>

    <script src="script.js"></script>
</body>
</html>




const express = require('express');
const multer = require('multer');
const VisualRecognitionV3 = require('ibm-watson/visual-recognition/v3');
const { IamAuthenticator } = require('ibm-watson/auth');

const app = express();
const port = 3000;

// Configure multer for file uploads
const storage = multer.memoryStorage();
const upload = multer({ storage: storage });

// Set up IBM Visual Recognition service
const visualRecognition = new VisualRecognitionV3({
  version: '2018-03-19',
  authenticator: new IamAuthenticator({
    apikey: 'YOUR_API_KEY',
  }),
  url: 'https://api.us-south.visual-recognition.watson.cloud.ibm.com',
});

// Handle file upload and recognition
app.post('/upload', upload.single('image'), async (req, res) => {
  try {
    const params = {
      images_file: req.file.buffer,
      classifier_ids: ['default'],
    };

    const response = await visualRecognition.classify(params);
    const classes = response.result.images[0].classifiers[0].classes;
    
    // You can generate captions based on the detected classes here

    res.json(classes);
  } catch (error) {
    console.error(error);
    res.status(500).json({ error: 'An error occurred' });
  }
});

app.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});
