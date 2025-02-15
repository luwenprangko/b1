```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Universal Scroll-to-Type with Undo (Desktop & Mobile)</title>
  <style>
    html, body {
      height: 100%;
      margin: 0;
      font-family: "Times New Roman", serif;
      overflow: hidden;
    }
    main {
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
    }
    /* Container fills the entire viewport height */
    .container {
      width: 100%;
      height: calc(100vh - 40px);
      max-width: 400px;
      border: 1px solid #ccc;
      margin: 20px;
      padding: 20px;
      box-sizing: border-box;
      display: flex;
      flex-direction: column;
    }
    /* Content area that scrolls if needed */
    #content {
      flex: 1;
      overflow-x: hidden;
      overflow-y: auto;
      white-space: pre-wrap;
      text-align: justify;
      background: #fff;
      box-sizing: border-box;
    }
  </style>
</head>
<body>
  <main>
    <div class="container">
      <div id="content"></div>
    </div>
  </main>

  <script>
    // Your long text
    const fullText =
      `Beloved one,

Thank you sa lahat ng mga bagay na nagawa mo para pasayahin ako, hindi ko man mavocal kung gaano ako nagpapasalamat, gagawa at gagawa ako ng paraan upang maparamdam ko sayong lahat ng bagay na naiibigay mo ay lubos akong nagpapasalamat, mula sa pagbake ng mga favorite pastries ko, sa pagluluto ng baon ko. Hindi ko man matumbasan lahat ng mga nagagawa mo, hindi pwedeng hindi ako gagawa ng paraan para makabawi sa lahat ng mga nagagawa mo. Sa simpleng sulat na to sana maiparating ko sayo ang lubos kong pagpapasalamat at lubos na pagmamahal.

Naging mahirap man ang pinagdaanan natin nitong mga nakalipas na araw, madalas man tayong nagkakasigawa, at hindi nagkakaunawaan, palagi pa ding ikaw ang laman ng pagmamahal ko, ikaw pa din ang hinahanap hanap ko sa gitna ng mga problema natin. Nangako ako sayo na sa kahit na anong bagay, ikaw pa din ang gugustuhin kong makasama sa habang buhay. Masaya ako sayo e, walang dahilan at walang hahadlang na mahalin at piliin ka kahit sa pinaka mahirap na sitwasyon nating dalawa. Hindi man kita nauunawaan paminsan minsan, handa pa rin akong intindihin ka kasi ikaw lang din naman ang nagiging kakampi ko kapag magisa ako, ikaw lang din naman ang nakakakilala sa kung sino ako. Kaya nagnangako akong babaguhin ko ang bagay na hindi dapat ako ganon sayo.

Sana, ako pa din ang piliin mong pakasalan, sana ako pa din ang piliin mong makasama sa habang buhay. 50 years from now, para pading 1 week old ang pagmamahal kong ibibigay sayo.

Mahal na mahal kita palagi, Lakambini

Eternally yours, Owen
      `;

    const contentDiv = document.getElementById("content");
    let accumulatedLetters = 0;
    let lettersRevealed = 0;
    let lastTouchY = null;

    // Update displayed text based on accumulatedLetters
    function updateText() {
      if (accumulatedLetters < 0) accumulatedLetters = 0;
      if (accumulatedLetters > fullText.length) accumulatedLetters = fullText.length;
      const newLetterCount = Math.floor(accumulatedLetters);
      if (newLetterCount !== lettersRevealed) {
        lettersRevealed = newLetterCount;
        contentDiv.textContent = fullText.substring(0, lettersRevealed);
        contentDiv.scrollTop = contentDiv.scrollHeight;
      }
    }

    // Convert scroll delta to letter increments
    function handleDelta(deltaY) {
      const computedStyle = window.getComputedStyle(contentDiv);
      const fontSize = parseFloat(computedStyle.fontSize);
      const lineHeight = fontSize * 1.2;
      const sensitivity = 4;
      const letterIncrement = (deltaY / lineHeight) * sensitivity;
      accumulatedLetters += letterIncrement;
      updateText();
    }

    // Desktop: Listen to the wheel event.
    window.addEventListener("wheel", (e) => {
      e.preventDefault();
      let deltaY = 0;
      if (e.deltaMode === WheelEvent.DOM_DELTA_PIXEL) {
        deltaY = e.deltaY;
      } else if (e.deltaMode === WheelEvent.DOM_DELTA_LINE) {
        const computedStyle = window.getComputedStyle(contentDiv);
        const fontSize = parseFloat(computedStyle.fontSize);
        deltaY = e.deltaY * (fontSize * 1.2);
      } else if (e.deltaMode === WheelEvent.DOM_DELTA_PAGE) {
        deltaY = e.deltaY * contentDiv.offsetHeight;
      }
      handleDelta(deltaY);
    }, { passive: false });

    // Mobile: Listen to touch events for vertical scrolling.
    window.addEventListener("touchstart", (e) => {
      if (e.touches.length === 1) {
        lastTouchY = e.touches[0].clientY;
      }
    }, { passive: false });

    window.addEventListener("touchmove", (e) => {
      if (e.touches.length === 1 && lastTouchY !== null) {
        const currentY = e.touches[0].clientY;
        const deltaY = lastTouchY - currentY;
        lastTouchY = currentY;
        e.preventDefault();
        handleDelta(deltaY);
      }
    }, { passive: false });

    window.addEventListener("touchend", () => {
      lastTouchY = null;
    }, { passive: false });
  </script>
</body>
</html>
```
