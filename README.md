[local-font-access-playground](https://dirkarnez.github.io/local-font-access-playground)
========================================================================================
From https://github.com/GoogleChrome/samples/tree/gh-pages/local-font-access

- https://developer.mozilla.org/en-US/docs/Web/API/CSS_Font_Loading_API
- ```js
  async function loadCustomLocalFonts() {
    // Check if the browser supports the Local Font Access API
    if (!("queryLocalFonts" in window)) {
      console.error("Local Font Access API is not supported in this browser.");
      return;
    }
  
    try {
      // Request permission and fetch all installed fonts
      const availableFonts = await window.queryLocalFonts();
  
      // Filter specifically for Times New Roman and PMingLiU / PMingLiU-ExtB
      const targetFonts = availableFonts.filter(font =>
        font.family === "Times New Roman" ||
        font.family === "PMingLiU" ||
        font.family === "PMingLiU-ExtB"
      );
  
      for (const fontData of targetFonts) {
        // Get the raw binary font file (Blob)
        const blob = await fontData.blob();
        const buffer = await blob.arrayBuffer();
  
        // Set unicode-range: English for Times New Roman, Chinese for PMingLiU
        const range = fontData.family === "Times New Roman"
          ? "U+0000-00FF"
          : "U+4E00-9FFF, U+3400-4DBF, U+20000-2A6DF, U+3000-303F, U+FF00-FFEF";
  
        // Register the font dynamically using the CSS Font Loading API
        const fontFace = new FontFace("CustomMixedFont", buffer, {
          unicodeRange: range,
          style: fontData.style.toLowerCase().includes("italic") ? "italic" : "normal",
          weight: fontData.style.toLowerCase().includes("bold") ? "bold" : "normal"
        });
  
        await fontFace.load();
        document.fonts.add(fontFace);
      }
  
      // Apply the newly registered font to the document
      document.body.style.fontFamily = '"CustomMixedFont", serif';
      console.log("Local fonts loaded and applied.");
  
    } catch (err) {
      console.error("Font access denied or failed:", err);
    }
  }
  
  // Trigger via a user click (required by browser security)
  document.getElementById("load-fonts-btn").addEventListener("click", loadCustomLocalFonts);
  ```
- ```html
  <!DOCTYPE html>
  <head>
  <style>
  @font-face {
    font-family: "Times New Roman";
    src: local("Times New Roman");
    unicode-range: U+0000-00FF;
  }
  
  /* 2. Use PMingLiU-ExtB (and standard PMingLiU) for Chinese characters */
  @font-face {
    font-family: "PMingLiU-ExtB";
    src: local("PMingLiU"), local("PMingLiU-ExtB");
    unicode-range: U+4E00-9FFF, U+3400-4DBF, U+20000-2A6DF, U+3000-303F, U+FF00-FFEF;
  }
  
  </style>
  </head>
  <body>
  
  <p class="p1">This is a paragraph, shown in the Times New Roman font.你</p>
  
  </body>
  </html>
  
  
  ```
