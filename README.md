# AI-integrated-Cybersecurity-threats-analyzer
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Cybersecurity Solver</title>
    <style>
    header {
        background-color: #2c3e50;
        color: white;
        text-align: center;
        padding: 2em 0;
        margin-bottom: 20px;
    }

    header img {
        max-width: 900px; /* Adjust logo size */
        height: auto;
        margin-bottom: 15px;
    }

    header h1 {
        margin: 0;
        font-size: 2.5em;
    }

    header p {
        font-size: 1.1em;
    }

    main {
        padding: 20px;
        max-width: 960px;
        margin: 0 auto;
        background-color: white;
        padding: 30px;
        border-radius: 8px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }

    #input-area h2, #result-area h2, #solution-area h2 {
        color: #2c3e50;
        margin-bottom: 15px;
    }

    .input-group {
        margin-bottom: 15px;
    }

    .input-group label {
        display: block;
        margin-bottom: 5px;
        font-weight: bold;
        color: #555;
    }

    .input-group input[type="text"],
    .input-group textarea,
    .input-group select,
    .input-group input[type="file"] {
        width: 100%;
        padding: 10px;
        border: 1px solid #ddd;
        border-radius: 4px;
        box-sizing: border-box;
        font-size: 1em;
    }

    .input-group textarea {
        resize: vertical;
    }

    #startVoice {
        background-color: #3498db;
        color: white;
        padding: 10px 15px;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        font-size: 1em;
    }

    #startVoice.recording {
        background-color: #e74c3c;
    }

    #analyzeButton, #solveButton {
        background-color: #5cb85c;
        color: white;
        padding: 12px 20px;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        font-size: 1.1em;
        transition: background-color 0.3s ease;
        margin-top: 10px;
        margin-right: 10px;
    }

    #analyzeButton:hover, #solveButton:hover {
        background-color: #4cae4c;
    }

    .loading {
        display: none;
        text-align: center;
        margin-top: 15px;
        font-style: italic;
        color: #777;
    }

    #analysisOutput, #solutionOutput {
        padding: 15px;
        border: 1px solid #eee;
        background-color: #f9f9f9;
        border-radius: 4px;
        white-space: pre-wrap; /* Preserve formatting */
        margin-bottom: 15px;
    }

    #solution-area {
        display: none;
        margin-top: 20px;
    }

    footer {
        text-align: center;
        padding: 1em 0;
        background-color: #2c3e50;
        color: white;
        position: fixed;
        bottom: 0;
        width: 100%;
        font-size: 0.9em;
    }
</style>
</head>
<body>
    <header>
        <img src="c:\Users\Admin\Desktop\images.jfif" alt="Cybersecurity Banner" style="width:300%; height:200px;">
        <h1>AI Integrated Cybersecurity Threat Analyzer</h1>
        <p>Analyze various threats like malware, phishing, and viruses.</p>
    </header>

    <main>
        <section id="input-area">
            <h2>Analyze Cybersecurity Data</h2>
            <div class="input-group">
                <label for="threatType">Select Threat Type:</label>
                <select id="threatType">
                    <option value="general">General Analysis</option>
                    <option value="malware">Malware Analysis (Code Snippet)</option>
                    <option value="phishing">Phishing Analysis (Email/URL)</option>
                    <option value="virus">Virus Analysis (Code Snippet)</option>
                    <option value="website">Website Code Analysis</option>
                </select>
            </div>
            <div class="input-group">
                <label for="inputData">Enter Data for Analysis:</label>
                <textarea id="inputData" rows="5" placeholder="Paste code, email content, URL, etc."></textarea>
            </div>
            <div class="input-group">
                <label for="imageUpload">Upload Image (e.g., Screenshot of suspicious email):</label>
                <input type="file" id="imageUpload" accept="image/*">
                <img id="imagePreview" src="#" alt="Image Preview" style="display: none; max-width: 200px; margin-top: 10px;">
            </div>
            <div class="input-group">
                <label for="voiceInput">Speak About the Attack:</label>
                <button id="startVoice">🎤 Start Voice Input</button>
                <p id="voiceTranscript"></p>
            </div>
            <button id="analyzeButton">Analyze Threat</button>
            <button id="solveButton" style="display: none;">Solve Threat</button>
            <div class="loading" id="loading">Analyzing...</div>
        </section>
    
        <section id="result-area" style="display: none;">
            <h2>Analysis Result</h2>
            <div id="analysisOutput"></div>
        </section>
    
        <section id="solution-area" style="display: none;">
            <h2>Suggested Solution</h2>
            <div id="solutionOutput"></div>
        </section>
    </main>
    
    <footer>
        <p>&copy; 2025 AI Cybersecurity Threat Analyzer</p>
    </footer>
    
    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const threatTypeSelect = document.getElementById('threatType');
            const inputDataTextarea = document.getElementById('inputData');
            const imageUploadInput = document.getElementById('imageUpload');
            const imagePreview = document.getElementById('imagePreview');
            const startVoiceButton = document.getElementById('startVoice');
            const voiceTranscriptParagraph = document.getElementById('voiceTranscript');
            const analyzeButton = document.getElementById('analyzeButton');
            const solveButton = document.getElementById('solveButton');
            const loadingDiv = document.getElementById('loading');
            const resultArea = document.getElementById('result-area');
            const analysisOutputDiv = document.getElementById('analysisOutput');
            const solutionArea = document.getElementById('solution-area');
            const solutionOutputDiv = document.getElementById('solutionOutput');
    
            let recognition;
            let isRecording = false;
            let uploadedImageBase64 = null;
            let voiceTranscript = '';
            let currentAnalysisResult = null; // Store the analysis result
    
            // Image Preview Functionality
            imageUploadInput.addEventListener('change', function() {
                const file = this.files[0];
                if (file) {
                    const reader = new FileReader();
                    reader.onload = function(e) {
                        uploadedImageBase64 = e.target.result;
                        imagePreview.src = uploadedImageBase64;
                        imagePreview.style.display = 'block';
                    };
                    reader.readAsDataURL(file);
                } else {
                    uploadedImageBase64 = null;
                    imagePreview.src = '#';
                    imagePreview.style.display = 'none';
                }
            });
    
            // Voice Input Functionality
            if ('webkitSpeechRecognition' in window || 'SpeechRecognition' in window) {
                const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
                recognition = new SpeechRecognition();
                recognition.continuous = false;
                recognition.interimResults = true;
    
                recognition.onstart = function() {
                    isRecording = true;
                    startVoiceButton.textContent = '🔴 Stop Voice Input';
                    startVoiceButton.classList.add('recording');
                    voiceTranscript = ''; // Clear previous transcript
                    voiceTranscriptParagraph.textContent = '';
                };
    
                recognition.onresult = function(event) {
                    let interimTranscript = '';
                    for (let i = event.resultIndex; i < event.results.length; ++i) {
                        if (event.results[i].isFinal) {
                            voiceTranscript += event.results[i][0].transcript + ' ';
                        } else {
                            interimTranscript += event.results[i][0].transcript;
                        }
                    }
                    voiceTranscriptParagraph.textContent = 'Speaking: ' + interimTranscript;
                };
    
                recognition.onerror = function(event) {
                    console.error('Speech recognition error:', event.error);
                    isRecording = false;
                    startVoiceButton.textContent = '🎤 Start Voice Input';
                    startVoiceButton.classList.remove('recording');
                };
    
                recognition.onend = function() {
                    isRecording = false;
                    startVoiceButton.textContent = '🎤 Start Voice Input';
                    startVoiceButton.classList.remove('recording');
                    voiceTranscriptParagraph.textContent = 'Voice Input: ' + voiceTranscript.trim();
                };
    
                startVoiceButton.addEventListener('click', function() {
                    if (isRecording) {
                        recognition.stop();
                    } else {
                        recognition.start();
                    }
                });
            } else {
                startVoiceButton.disabled = true;
                startVoiceButton.textContent = 'Voice Input Not Supported';
            }
    
            // Simulate AI Analysis
            analyzeButton.addEventListener('click', function() {
                const threatType = threatTypeSelect.value;
                const inputData = inputDataTextarea.value;
                loadingDiv.style.display = 'block';
                resultArea.style.display = 'none';
                solutionArea.style.display = 'none';
                analysisOutputDiv.innerHTML = '';
                solutionOutputDiv.innerHTML = '';
                solveButton.style.display = 'none';
    
                // Simulate a delay for analysis
                setTimeout(function() {
                    loadingDiv.style.display = 'none';
                    currentAnalysisResult = simulateAIAnalysis(threatType, inputData, uploadedImageBase64, voiceTranscript);
                    analysisOutputDiv.innerHTML = currentAnalysisResult.analysis;
                    resultArea.style.display = 'block';
                    if (currentAnalysisResult.potentialThreat) {
                        solveButton.style.display = 'inline-block';
                    }
                }, 2000);
            });
    
            // Simulate AI Solution Generation
            solveButton.addEventListener('click', function() {
                if (currentAnalysisResult && currentAnalysisResult.potentialThreat) {
                    loadingDiv.style.display = 'block';
                    solutionArea.style.display = 'none';
                    solutionOutputDiv.innerHTML = '';
    
                    setTimeout(function() {
                        loadingDiv.style.display = 'none';
                        const solution = simulateAISolution(currentAnalysisResult.threatType, currentAnalysisResult.details);
                        solutionOutputDiv.innerHTML = solution;
                        solutionArea.style.display = 'block';
                    }, 1500);
                } else {
                    alert('No potential threat identified, cannot provide a solution.');
                }
            });
    
            function simulateAIAnalysis(threatType, inputData, imageData, voiceData) {
                let analysis = '<p><b>Analysis Type:</b> ' + threatType + '</p>';
                let potentialThreat = false;
                let threatDetails = {};
    
                if (inputData) {
                    analysis += '<p><b>Input Data:</b><pre>' + sanitize(inputData) + '</pre></p>';
                }
                if (imageData) {
                    analysis += '<p><b>Image Data:</b> Image uploaded for context.</p>';
                }
                if (voiceData) {
                    analysis += '<p><b>User Voice Input:</b> ' + sanitize(voiceData) + '</p>';
                }
                analysis += '<hr>';
    
                switch (threatType) {
                    case 'malware':
                        if (inputData.toLowerCase().includes('eval(') || inputData.toLowerCase().includes('unescape(')) {
                            analysis += '<p><span style="color: red;"><b>Potential Malware:</b></span> Suspicious JavaScript functions detected.</p>';
                            potentialThreat = true;
                            threatDetails = { type: 'malware', description: 'Suspicious JavaScript functions found.' };
                        } else if (inputData.toLowerCase().includes('<script>')) {
                            analysis += '<p><span style="color: orange;"><b>Possible Script Injection:</b></span> JavaScript tags found.</p>';
                            threatDetails = { type: 'script_injection', description: 'JavaScript tags detected.' };
                        } else {
                            analysis += '<p><span style="color: green;"><b>Analysis:</b></span> No immediate high-risk patterns for generic malware found in the code snippet.</p>';
                        }
                        break;
                    case 'phishing':
                        if (inputData.toLowerCase().includes('urgent action required') || inputData.toLowerCase().includes('verify your account') || inputData.toLowerCase().includes('prize')) {
                            analysis += '<p><span style="color: red;"><b>Potential Phishing:</b></span> Common phishing keywords detected.</p>';
                            potentialThreat = true;
                            threatDetails = { type: 'phishing', description: 'Common phishing keywords found.' };
                        } else if (inputData.startsWith('http') && !isValidDomain(inputData)) {
                            analysis += '<p><span style="color: orange;"><b>Possible Suspicious URL:</b></span> The domain might be unusual.</p>';
                            threatDetails = { type: 'suspicious_url', description: 'Unusual domain detected.' };
                        } else {
                            analysis += '<p><span style="color: green;"><b>Analysis:</b></span> No strong indicators of phishing detected in the provided content.</p>';
                        }
                        break;
                    case 'virus':
                        if (inputData.toLowerCase().includes('function infect(') || inputData.toLowerCase().includes('self.replicate()')) {
                            analysis += '<p><span style="color: red;"><b>Potential Virus Behavior:</b></span> Keywords suggesting replication or infection found.</p>';
                            potentialThreat = true;
                            threatDetails = { type: 'virus', description: 'Keywords suggesting replication or infection found.' };
                        } else {
                            analysis += '<p><span style="color: green;"><b>Analysis:</b></span> No immediate signs of virus-like behavior detected in the code.</p>';
                        }
                        break;
                    case 'website':
                        if (inputData.toLowerCase().includes('<iframe src=') || inputData.toLowerCase().includes('onclick=')) {
                            analysis += '<p><span style="color: orange;"><b>Possible Website Vulnerabilities:</b></span> Use of iframes or inline event handlers might introduce risks.</p>';
                            threatDetails = { type: 'website_vulnerability', description: 'Use of iframes or inline event handlers detected.' };
                        } else if (inputData.toLowerCase().includes('<script src="//')) {
                            analysis += '<p><span style="color: blue;"><b>External Script Inclusion:</b></span> Review the source of external scripts for security.</p>';
                            threatDetails = { type: 'external_script', description: 'Inclusion of external scripts found.' };
                        } else {
                            analysis += '<p><span style="color: green;"><b>Analysis:</b></span> No immediate critical vulnerabilities easily identified in the website code.</p>';
                        }
                        break;
                    default:
                        analysis += '<p><span style="color: blue;"><b>Analysis:</b></span> Performing a general analysis. No specific threat patterns strongly identified.</p>';
                }
    
                return { analysis: analysis, potentialThreat: potentialThreat, threatType: threatType, details: threatDetails };
            }
    
            function simulateAISolution(threatType, details) {
                let solution = '<p>Based on the analysis of a potential <b>' + threatType + '</b>:</p><hr>';
                switch (threatType) {
                    case 'malware':
                        solution += '<p><b>Recommended Actions:</b></p>';
                        solution += '<ul>';
                        solution += '<li>Isolate the affected system immediately to prevent further spread.</li>';
                        solution += '<li>Run a full scan with a reputable anti-malware software.</li>';
                        solution += '<li>Review and remove any suspicious code or applications.</li>';
                        solution += '<li>Consider restoring the system from a clean backup if necessary.</li>';
                        solution += '<li>Educate users about safe browsing and email practices to prevent future infections.</li>';
                        solution += '</ul>';
                        break;
                    case 'phishing':
                        solution += '<p><b>Recommended Actions:</b></p>';
                        solution += '<ul>';
                        solution += '<li>Do not click on any links or provide personal information in response to the email.</li>';
                        solution += '<li>Report the phishing attempt to your email provider for further investigation.</li>';
                        solution += '<li>Implement email filtering solutions that can help identify and block phishing emails.</li>';
                        solution += '<li>Educate users on how to recognize phishing attempts and suspicious emails.</li>';
                        solution += '</ul>';
                        break;
                    case 'virus':
                        solution += '<p><b>Recommended Actions:</b></p>';
                        solution += '<ul>';
                        solution += '<li>Disconnect the infected device from the network immediately.</li>';
                        solution += '<li>Run a full antivirus scan and follow the software’s recommendations for removal.</li>';
                        solution += '<li>Change passwords for sensitive accounts, especially if they may have been compromised.</li>';
                        solution += '<li>Keep your operating system and software updated to prevent vulnerabilities.</li>';
                        solution += '</ul>';
                        break;
                    case 'website':
                        solution += '<p><b>Recommended Actions:</b></p>';
                        solution += '<ul>';
                        solution += '<li>Review code for any vulnerabilities identified, such as insecure user input handling.</li>';
                        solution += '<li>Implement Content Security Policy (CSP) to mitigate risks from external scripts.</li>';
                        solution += '<li>Consider conducting a thorough security audit of the website.</li>';
                        solution += '<li>Educate developers about secure coding practices to prevent future vulnerabilities.</li>';
                        solution += '</ul>';
                        break;
                    default:
                        solution += '<p>No specific actions recommended as no potential threat was identified.</p>';
                }
                return solution;
            }

            function isValidDomain(url) {
                // Simple domain validation
                const domainRegex = /^(http|https):\/\/([a-z0-9-]+\.)+[a-z]{2,}/i;
                return domainRegex.test(url);
            }

            function sanitize(input) {
                // Simple sanitization to prevent XSS
                return input.replace(/</g, "&lt;").replace(/>/g, "&gt;");
            }
        });
    </script>
</body>
</html>
