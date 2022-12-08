<script>
  import * as Tone from 'tone';

  let osc;
  let started = false;
  let engaged = false;

  let height = window.innerHeight;
  let width = window.innerWidth;

  let oscillatorTypes = ['sine', 'triangle', 'square', 'sawtooth'];
  let oscSelected;

  let tremoloOptions = {
    frequency: 9,
    depth: .75
  }

  const startAudio = () => {
    started = true;

  }

  const getVolume = (x, width) => {
    let percentVolume = x / width;
    let percentRemove = 1 - percentVolume;
    let actualVolume = -20 - (26 * percentRemove);
    return actualVolume;
  }

  const getFrequency = (y, height) => {
    return (height - y) + 200;
  }

  const engageAudio = (e) => {
    if (!started || engaged) return;
    console.log(e)
    engaged = true;
    let x = e.offsetX || e.targetTouches[0].clientX;
    let y = e.offsetY || e.targetTouches[0].clientY;

    let width = e.target.clientWidth;
    let height = e.target.clientHeight;

    const tremolo = new Tone.Tremolo(tremoloOptions.frequency, tremoloOptions.depth).toDestination().start();

    osc = new Tone.Oscillator({
      type: oscSelected,
      frequency: getFrequency(y, height),
      volume: getVolume(x, width),
    }).connect(tremolo);
    osc.start();
  }

  const disengageAudio = () => {
    if (engaged) {
      engaged = false;
      osc.mute = true;
      console.log(osc)
    } 
  }

  const moveCursor = (e) => {
    if (engaged) {
      let x = e.offsetX || e.targetTouches[0].clientX;
      let y = e.offsetY || e.targetTouches[0].clientY;

      let width = e.target.clientWidth;
      let height = e.target.clientHeight;

      osc.set({
        frequency: getFrequency(y, height),
        volume: getVolume(x, width)
      })
    }
  }

</script>

<!-- <svelte:window 
  on:mousemove={moveCursor}
  on:mousedown={engageAudio}
  on:mouseup={disengageAudio}

  on:touchmove={moveCursor}
  on:touchstart={engageAudio}
  on:touchend={disengageAudio}
  /> -->
<main>
  {#if started}
  
  <div
  on:mousemove={moveCursor}
  on:mousedown={engageAudio}
  on:mouseup={disengageAudio}
  on:mouseleave={disengageAudio}

  on:touchmove={moveCursor}
  on:touchstart={engageAudio}
  on:touchend={disengageAudio}
  style='height: 70vh'
  class='field'
  ></div>
  <div class='controls'>


    <select bind:value={oscSelected}>
      {#each oscillatorTypes as osc}
      <option value={osc}>{osc.toUpperCase()}</option>
      {/each}
    </select>
  
    <p>Tremolo</p>
    <div>
      <input type="range" id="frequency" name="frequency"
             min="0" max="100" bind:value={tremoloOptions.frequency}>
      <label for="frequency">Frequency</label>
    </div>
    
    <div>
      <input type="range" id="depth" name="depth" 
             min="0" max="1" bind:value={tremoloOptions.depth} step=".01">
      <label for="depth">Depth</label>
    </div>
  </div>

  {:else}
    <h1>Click</h1>
    <button on:click|once={startAudio}>HERE</button>
  {/if}
</main>

<style>

  * {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
  }

 :root {
        color: white;
        background-color: black;
        text-align: center;
        cursor: crosshair;
        user-select: none;
      }

      main {
        max-height: 90vh;
      }

      .field {
        outline: 1px solid red;
        width: 100vw;
      }

      .controls {
        height: 20vh;
      }

      h1 {
        margin-top: 30vh;
      }
</style>
