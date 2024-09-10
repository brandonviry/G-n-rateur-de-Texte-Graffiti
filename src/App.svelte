<script lang="ts">


  let text: string = "Hello world!";
  let classNames: string = "";
  let taille: number = 50;
  let color: string = "#000";
  let bgcolor: string = "#fff";
  let paddingX: number = 10;
  let paddingY: number = 10;
  let margin: number = 10;


  let fontFamily: string = "Arial";
  let googleFonts: string[] = [
    "Roboto", "Open Sans", "Lato", "Montserrat", "Raleway", 
    "Poppins", "Oswald", "Source Sans Pro", "Slabo 27px", "Merriweather",
    "PT Sans", "Noto Sans", "Playfair Display", "Ubuntu", "Roboto Condensed",
    "Roboto Slab", "Nunito", "Titillium Web", "Mukta", "Lora"
  ];
  let fontWeight: number = 400;
  let letterSpacing: number = 0;

  let outlineColor: string = "#000000";
  let glowColor: string = "#FF00FF";
  let rotation: number = 0;
  let sprayEffect: number = 0;

  let graffitiEffects = [
    { name: "Contour", class: "contour", active: false, color: "#000000", width: 2 },
    { name: "Lueur", class: "glow", active: false, color: "#00FFFF", intensity: 10 },
    { name: "Spray", class: "spray", active: false, intensity: 5 },
    { name: "Incliné", class: "tilted", active: false, angle: -5 },
    { name: "Ombre", class: "shadow", active: false, color: "#000000", offset: 3 },
    { name: "Dégradé", class: "gradient", active: false, color1: "#ff00ff", color2: "#00ffff" }
  ];

  function updateClassNames() {
    classNames = graffitiEffects
      .filter(effect => effect.active)
      .map(effect => effect.class)
      .join(" ");
  }

  $: cssVariables = graffitiEffects.reduce((acc, effect) => {
    if (effect.active) {
      switch (effect.class) {
        case "contour":
          acc += `--contour-color: ${effect.color}; --contour-width: ${effect.width}px;`;
          break;
        case "glow":
          acc += `--glow-color: ${effect.color}; --glow-intensity: ${effect.intensity}px;`;
          break;
        case "spray":
          acc += `--spray-intensity: ${effect.intensity};`;
          break;
        case "tilted":
          acc += `--tilt-angle: ${effect.angle}deg;`;
          break;
        case "shadow":
          acc += `--shadow-color: ${effect.color}; --shadow-offset: ${effect.offset}px;`;
          break;
        case "gradient":
          acc += `--gradient-color1: ${effect.color1}; --gradient-color2: ${effect.color2};`;
          break;
      }
    }
    return acc;
  }, "");

</script>

<main>
  <div class="layout">
    <div class="preview">
      <section id="display">
        <h1 id="text" class={classNames} style="
          font-size: {taille}px; 
          color: {color}; 
          background-color: {bgcolor}; 
          padding: {paddingY}px {paddingX}px; 
          margin: {margin}px; 
          font-family: {fontFamily};
          font-weight: {fontWeight};
          letter-spacing: {letterSpacing}px;
          --outline-color: {outlineColor};
          --glow-color: {glowColor};
          --rotation: {rotation}deg;
          {cssVariables}
        ">{text}</h1>
      </section>
    </div>
    <div class="controls">
      <section>
        <input bind:value={text} />
      </section>

      <section>
        {#each ["ombreporter", "elevation", "glow"] as className}
          <label for={className}>{className}</label>
          <input type="checkbox" id={className}  on:change={(e)=>{classNames = e.target.checked ? classNames +" "+ className : classNames.replace(className, "")}}/>
        {/each}
        <label for="taille">taille</label>
        <input type="range" min="0" max="100" bind:value={taille} name="taille" id="taille">

        <label for="color">color</label>
        <input type="color" name="color" id="color" bind:value={color}>

        <label for="bgcolor">bgcolor</label>
        <input type="color" name="bgcolor" id="bgcolor" bind:value={bgcolor}>

        <label for="padding">padding X</label>
        <input type="number" min="0" max="100" bind:value={paddingX}>
        <label for="padding">padding Y</label>
        <input type="number" min="0" max="100" bind:value={paddingY}>

        <label for="margin">margin</label>
        <input type="number" min="0" max="100" bind:value={margin}>
        <select on:change={(e) => classNames += " " + e.target.value}>
          {#each ["border-full", "border-lg", "border-md", "border-sm", "border-xs"] as className}
              <option value={className}>{className}</option>
          {/each}
        </select>
      </section>

      <section>
        <label for="fontFamily">Police</label>
        <select bind:value={fontFamily}>
          {#each ["Arial", "Helvetica", "Georgia", "Times New Roman", "Courier New", ...googleFonts] as font}
            <option value={font}>{font}</option>
          {/each}
        </select>

        <label for="fontWeight">Épaisseur</label>
        <input type="range" min="100" max="900" step="100" bind:value={fontWeight}>

        <label for="letterSpacing">Espacement des lettres</label>
        <input type="range" min="-5" max="10" bind:value={letterSpacing}>
      </section>

      <section class="graffiti-effects">
        <h3>Effets de graffiti</h3>
        <div class="effects-grid">
          {#each graffitiEffects as effect}
            <div class="effect-card {effect.active ? 'active' : ''}">
              <label class="effect-toggle">
                <input type="checkbox" bind:checked={effect.active} on:change={updateClassNames}>
                <span class="effect-name">{effect.name}</span>
              </label>
              {#if effect.active}
                <div class="effect-controls">
                  {#if effect.class === "contour" || effect.class === "glow" || effect.class === "shadow"}
                    <input type="color" bind:value={effect.color} title="Couleur">
                  {/if}
                  {#if effect.class === "contour"}
                    <input type="range" bind:value={effect.width} min="1" max="10" step="1" title="Épaisseur">
                  {:else if effect.class === "glow"}
                    <input type="range" bind:value={effect.intensity} min="1" max="20" step="1" title="Intensité">
                  {:else if effect.class === "spray"}
                    <input type="range" bind:value={effect.intensity} min="1" max="10" step="0.1" title="Intensité">
                  {:else if effect.class === "tilted"}
                    <input type="range" bind:value={effect.angle} min="-45" max="45" step="1" title="Angle">
                  {:else if effect.class === "shadow"}
                    <input type="range" bind:value={effect.offset} min="1" max="10" step="1" title="Décalage">
                  {:else if effect.class === "gradient"}
                    <input type="color" bind:value={effect.color1} title="Couleur 1">
                    <input type="color" bind:value={effect.color2} title="Couleur 2">
                  {/if}
                </div>
              {/if}
            </div>
          {/each}
        </div>
      </section>
    </div>
  </div>
</main>

<style>
  #display {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    border: 1px solid #ccc;
    border-radius: 4px;
    width: 90%;
  }

  input {
    width: 100%;
    padding: 10px 10px;
    margin: 10px 0;
    box-sizing: border-box;
    border: 1px solid #ccc;
    border-radius: 4px;
  }

  .ombreporter {
    text-shadow: 5px 5px 10px rgba(0, 0, 0, 0.5);
  }
  .elevation {
    box-shadow:
      0px 10px 15px rgba(0, 0, 0, 0.3),
      0px 15px 35px rgba(0, 0, 0, 0.2);
  }
  .glow {
    color: #fff;
    text-shadow:
      0 0 10px #00f,
      0 0 20px #00f,
      0 0 30px #00f;
  }
  .glitch {
    position: relative;
    font-size: 50px;
    color: white;
    text-shadow:
      1px 1px red,
      -1px -1px blue;
    animation: glitch-animation 1s infinite;
  }

  @keyframes glitch-animation {
    0% {
      transform: translate(0);
    }
    20% {
      transform: translate(-2px, 2px);
    }
    40% {
      transform: translate(2px, -2px);
    }
    60% {
      transform: translate(-1px, 1px);
    }
    80% {
      transform: translate(1px, -1px);
    }
    100% {
      transform: translate(0);
    }
  }

  .border-full { border-radius: 100%; }
  .border-lg { border-radius: 10px; }
  .border-md { border-radius: 5px; }
  .border-sm { border-radius: 3px; }
  .border-xs { border-radius: 1px; }

  main {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

section {
  background-color: #f0f0f0;
  border-radius: 8px;
  padding: 20px;
  margin-bottom: 20px;
}

label {
  display: block;
  margin-top: 10px;
  font-weight: bold;
}

select, input[type="color"] {
  width: 100%;
  padding: 5px;
  margin-top: 5px;
}

.graffiti {
  font-family: 'Arial', sans-serif;
  text-shadow: 
    -1px -1px 0 var(--outline-color),
    1px -1px 0 var(--outline-color),
    -1px 1px 0 var(--outline-color),
    1px 1px 0 var(--outline-color),
    0 0 5px var(--glow-color),
    0 0 10px var(--glow-color),
    0 0 15px var(--glow-color);
  transform: rotate(var(--rotation));
  filter: url(#spray-filter);
}

.contour {
  -webkit-text-stroke: var(--contour-width) var(--contour-color);
}

.spray {
  filter: url(#spray-filter);
}

.tilted {
  transform: rotate(var(--tilt-angle));
}

.shadow {
  text-shadow: var(--shadow-offset) var(--shadow-offset) 0px var(--shadow-color);
}

.gradient {
  background: linear-gradient(45deg, var(--gradient-color1), var(--gradient-color2));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.graffiti-effects {
  background-color: #f0f0f0;
  border-radius: 8px;
  padding: 20px;
  margin-top: 20px;
}

.effects-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 20px;
}

.effect-card {
  background-color: #ffffff;
  border-radius: 8px;
  padding: 15px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  transition: all 0.3s ease;
}

.effect-card.active {
  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
}

.effect-toggle {
  display: flex;
  align-items: center;
  cursor: pointer;
}

.effect-name {
  margin-left: 10px;
  font-weight: bold;
}

.effect-controls {
  margin-top: 10px;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

input[type="color"] {
  width: 100%;
  height: 30px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

input[type="range"] {
  width: 100%;
}

.layout {
  display: flex;
  gap: 20px;
}

.preview {
  flex: 1;
  position: sticky;
  top: 20px;
  height: calc(100vh - 40px);
  overflow-y: auto;
}

.controls {
  flex: 1;
  overflow-y: auto;
  max-height: calc(100vh - 40px);
}

</style>

<svg style="position: absolute; width: 0; height: 0;">
  <filter id="spray-filter">
    <feTurbulence type="fractalNoise" baseFrequency="0.05" numOctaves="3" seed="1" />
    <feDisplacementMap in="SourceGraphic" scale="{graffitiEffects.find(e => e.class === 'spray')?.intensity}" />
  </filter>
</svg>


