<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pakistan Railways Rules Book</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #000;
            color: #fff;
            margin: 0;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        h1 {
            text-align: center;
            margin-bottom: 20px;
        }
        .search-container {
            display: flex;
            flex-direction: column;
            gap: 10px;
            width: 100%;
            max-width: 600px;
            margin-bottom: 20px;
        }
        input[type="text"] {
            padding: 10px;
            font-size: 16px;
            border: 1px solid #333;
            background-color: #222;
            color: #fff;
            border-radius: 5px;
        }
        button {
            padding: 10px;
            font-size: 16px;
            background-color: #444;
            color: #fff;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        button:hover {
            background-color: #666;
        }
        #pdf-container {
            width: 100%;
            max-width: 800px;
            height: 600px;
            border: 1px solid #333;
            background-color: #111;
        }
        canvas {
            width: 100%;
            height: auto;
        }
        @media (max-width: 768px) {
            body {
                padding: 10px;
            }
            #pdf-container {
                height: 400px;
            }
        }
    </style>
</head>
<body>
    <h1>Pakistan Railways Rules Book</h1>
    <div class="search-container">
        <input type="text" id="search-input" placeholder="Search for word or rule number in PDF">
        <button onclick="searchPDF()">Search PDF</button>
        <input type="text" id="ai-search-input" placeholder="AI Search: Type topic like 'shunting rules'">
        <button onclick="aiSearch()">AI Search</button>
    </div>
    <div id="pdf-container"></div>

    <!-- PDF.js Library -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.min.js"></script>
    <script>
        const url = 'https://drive.google.com/file/d/1Wvt4VKqrEcpc6pQtoq2UY4DcHy-N2KzC/view?usp=drivesdk'; // Replace with actual PDF URL
        let pdfDoc = null;
        let pageNum = 1;
        let pageRendering = false;
        let pageNumPending = null;
        const scale = 1.5;
        const canvas = document.createElement('canvas');
        const ctx = canvas.getContext('2d');
        document.getElementById('pdf-container').appendChild(canvas);

        // Load PDF
        pdfjsLib.getDocument(url).promise.then(function(pdf) {
            pdfDoc = pdf;
            renderPage(pageNum);
        });

        // Render page
        function renderPage(num) {
            pageRendering = true;
            pdfDoc.getPage(num).then(function(page) {
                const viewport = page.getViewport({scale: scale});
                canvas.height = viewport.height;
                canvas.width = viewport.width;
                const renderContext = {
                    canvasContext: ctx,
                    viewport: viewport
                };
                const renderTask = page.render(renderContext);
                renderTask.promise.then(function() {
                    pageRendering = false;
                    if (pageNumPending !== null) {
                        renderPage(pageNumPending);
                        pageNumPending = null;
                    }
                });
            });
        }

        // Search PDF
        function searchPDF() {
            const query = document.getElementById('search-input').value.toLowerCase();
            if (!query) return;
            // Simple search: loop through pages and find text
            for (let i = 1; i <= pdfDoc.numPages; i++) {
                pdfDoc.getPage(i).then(function(page) {
                    page.getTextContent().then(function(textContent) {
                        const text = textContent.items.map(item => item.str).join(' ').toLowerCase();
                        if (text.includes(query)) {
                            pageNum = i;
                            renderPage(pageNum);
                            return;
                        }
                    });
                });
            }
        }

        // AI Search (simple keyword-based topic search)
        function aiSearch() {
            const topic = document.getElementById('ai-search-input').value.toLowerCase();
            const keywords = {
                'shunting rules': ['shunt', 'shunting', 'rule'],
                'safety rules': ['safety', 'secure', 'danger'],
                // Add more topics and keywords as needed
            };
            let searchTerm = '';
            for (const [key, words] of Object.entries(keywords)) {
                if (key.includes(topic) || words.some(word => topic.includes(word))) {
                    searchTerm = words.join(' ');
                    break;
                }
            }
            if (searchTerm) {
                document.getElementById('search-input').value = searchTerm;
                searchPDF();
            } else {
                alert('Topic not recognized. Try a different topic.');
            }
        }
    </script>
</body>
</html># General-Rules-P.R
