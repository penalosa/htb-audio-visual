<script>
  let canvas;
  let w, h;
  const onClick = () => {
    const audioCtx = new (window.AudioContext || window.webkitAudioContext)();

    let analyser = audioCtx.createAnalyser();

    navigator.mediaDevices
      .getUserMedia({
        audio: {
          echoCancellation: false,
          autoGainControl: false,
          noiseSuppression: false
        }
      })
      .then((stream) => {
        let source = audioCtx.createMediaStreamSource(stream);
        source.connect(analyser);
        analyser.connect(audioCtx.destination);

        analyser.fftSize = 1024;

        let dataArray = new Float32Array(analyser.fftSize);

        let canvasCtx = canvas.getContext("2d");

        const draw = () => {
          requestAnimationFrame(draw);

          analyser.getFloatTimeDomainData(dataArray);

          canvasCtx.clearRect(0, 0, w, h);
          canvasCtx.strokeStyle = "#fff";
          canvasCtx.fillStyle = "#fff";
          canvasCtx.lineWidth = 2;

          let x = 0;
          let sliceWidth = w / analyser.fftSize;

          canvasCtx.beginPath();
          canvasCtx.moveTo(0, h / 2);
          canvasCtx.closePath();

          for (let i = 0; i < analyser.fftSize; i++) {
            let value = (dataArray[i] * h) / 2;
            let y = h / 2 + value;
            canvasCtx.beginPath();

            canvasCtx.strokeStyle = `rgb(${
              Math.abs(dataArray[i]) * 255 * 2
            },100, 100)`;

            canvasCtx.moveTo(x, y);
            canvasCtx.lineTo(x, h - y);
            canvasCtx.stroke();

            canvasCtx.closePath();

            x += sliceWidth;
          }
          canvasCtx.beginPath();

          canvasCtx.lineTo(w, h / 2);

          canvasCtx.stroke();
          canvasCtx.closePath();
        };
        draw();
      });
  };
</script>

<div
  class="h-screen w-screen bg-gray-400"
  bind:clientHeight={h}
  bind:clientWidth={w}
  on:click={onClick}
>
  <canvas
    class="waveform absolute top-0"
    bind:this={canvas}
    width={w}
    height={h}
  />
</div>
