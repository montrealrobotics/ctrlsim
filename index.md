<!-- <div id="info">Description</div> -->
<!-- <canvas id="canvas">
</canvas>
<script type="module" src="./assets/renderer.js"></script> -->

<br>
<br>


## Demo

<div class="container ">
  <div class="row">
    <div class="col-3"></div>
    <div class="col-6">
      <div class="row">
        <div>Metric:</div>
        <div class="btn-group" role="group" aria-label="Basic radio toggle button group">
          
          <input type="radio" class="btn-check" name="btnradio" id="veh" autocomplete="off" checked>
          <label class="btn btn-outline-primary" for="veh">Vehicle-Vehicle</label>

          <input type="radio" class="btn-check" name="btnradio" id="edge" autocomplete="off">
          <label class="btn btn-outline-primary" for="edge">Vehicle-Edge</label>

          <input type="radio" class="btn-check" name="btnradio" id="goal" autocomplete="off">
          <label class="btn btn-outline-primary" for="goal">Vehicle-Goal</label>
        </div>
      </div>
      <div class="row mt-3">
        <label for="tiltslider" class="form-label" id="tiltsliderLabel">Tilting: Neutral (default)</label>
      </div>
      <div class="row">
        <input type="range" class="form-range" min="0" max="2" step="1" id="tiltslider">
      </div>
    </div>
    <div class="col-3"></div>
  </div>
  <div class="row">
    <div class="col-1"></div>
    <div class="col-10">
      <div class="row">
        <video id="myvideo" controls autoplay muted>
          <source id="vmp4" src="assets/vids/veh_veh_no_tilt/veh_veh_no_tilt.mp4" type="video/mp4">
          <p>Your browser does not support this video format.</p>
        </video>
      </div>
    </div>
    <div class="col-1"></div>
  </div>
</div>

<script>
  let tsl = document.getElementById("tiltsliderLabel");
  var vid = document.getElementById("myvideo");
  let tilt = "no"
  let scene = "veh_veh"
  document.getElementById("tiltslider").addEventListener('input', evt => {
    // console.log(evt.target.value)
    if (evt.target.value == 0) {
      tsl.innerHTML = "Tilting: Negative (ignoring instructions) 💥";
      tilt = "negative";
    } else if (evt.target.value == 1) {
      tsl.innerHTML = "Tilting: Neutral (default)";
      tilt = "no";
    } else {
      tsl.innerHTML = "Tilting: Positive (more eager)";
      tilt = "positive";
    }
    swapVid();
  });

  function swapScenario(evt) {
    // console.log(evt.target.id);
    if (evt.target.id == "veh") {
      scene = "veh_veh";
    } else if (evt.target.id == "edge") {
      scene = "veh_edge";
    } else {
      scene = "goal";
    }
    swapVid();
  }

  function swapVid() {
    let vpath = `assets/vids/${scene}_${tilt}_tilt/${scene}_${tilt}_tilt.mp4`;
    vid.setAttribute('src', vpath);
    vid.load();
  }
  document.querySelectorAll("input[name='btnradio']").forEach((input) => {
      input.addEventListener('change', swapScenario);
  });

  // var count = 1;
  // var vid = document.getElementById("myvideo");
  // vid.addEventListener("ended", switchvideo, false);
  // function switchvideo(e) {

  //   if (count % 2 === 0) {
  //     vid.setAttribute('src', 'v1.mp4');
  //   } else {
  //     vid.setAttribute('src', 'v2.mp4');
  //   }
  //   count++;
  //   vid.load();
  //   try {
  //     setTimeout(()=>vid.play(), 2000);
  //   } catch (err){
  //      console.log(err)
  //   }
  // }
</script>

TL;DR: We propose **CtRL-Sim**, a framework that leverages return-conditioned offline reinforcement learning (RL) to enable reactive, closed-loop, and controllable behaviour simulation within a physics-enhanced Nocturne environment. We propose a return-conditioned multi-agent encoder-decoder Transformer architecture within the CtRL- Sim framework to imitate the driving behaviours in a curated offline RL Waymo dataset. We then leverage exponential tilting of the predicted return distribution as a simple yet effective mechanism to control the simulated agent behaviours. CtRL-Sim enables the generation of a wide range of realistic driving behaviours beyond the scope of the initial dataset, including adversarial behaviours for the generation of safety-critical scenarios.

![](figures-fig1-v5.png "Nested Set Transformers Overview")

## Abstract

Evaluating autonomous vehicle stacks (AVs) in simulation typically involves replaying driving logs from real-world recorded traffic. However, agents replayed from offline data are not reactive and hard to intuitively control. Existing approaches address these challenges by proposing methods that rely on heuristics or generative models of real-world data but these approaches either lack realism or necessitate costly iterative sampling procedures to control the generated behaviours. In this work, we take an alternative approach and propose CtRL-Sim, a method that leverages return-conditioned offline reinforcement learning to efficiently generate reactive and controllable traffic agents. Specifically, we process real-world driving data through a physics-enhanced Nocturne simulator to generate a diverse offline reinforcement learning dataset, annotated with various reward terms. With this dataset, we train a return-conditioned multi-agent behaviour model that allows for fine-grained manipulation of agent behaviours by modifying the desired returns for the various reward components. This capability enables the generation of a wide range of driving behaviours beyond the scope of the initial dataset, including adversarial behaviours. We demonstrate that CtRL-Sim can generate diverse and realistic safety-critical scenarios while providing fine-grained control over agent behaviours.

## Architecture

![](overview.png)

We propose to learn a multi-agent return-conditioned policy, parameterized using a novel autoregressive encoder-decoder Transformer architecture, which can be scalably trained via next-token prediction. 

At inference time, we leverage exponential tilting to control the agent behaviours. Negative tilting values yield bad driving behaviours while positive tilting values yields good driving behaviours. This provides an interpretable and efficient mechanism for controlling agent behaviours, and circumvents the need for expensive iterative sampling processes such as Bayesian optimization or guided diffusion sampling.

## Examples

![](multi-agent-sim-website.gif)

CtRL-Sim can faithfully imitate the multi-agent driving behaviours found in the Waymo dataset. The teal agents are controlled by CtRL-Sim, and other agents in pink are set to log-replay. 

![](veh-veh-tilting-website.gif)

Exponential tilting enables control over the generation of realistic vehicle-vehicle collision scenarios. We show the evolution of three traffic scenes with the top panels showing the log-replay scenario through physics (shown in teal) and the bottom panels applying negative tilting to the CtRL-Sim- controlled agent. Bounding boxes outlined in red contain a traffic violation. All other agents are set to log-replay through physics, with the agent interacting with the CtRL-Sim-controlled agent denoted in pink. Goals are denoted by small circles. 

## Bibtex

    @inproceedings{
      rowe2024ctrlsim,
      title={CtRL-Sim: Reactive and Controllable Driving Agents with Offline Reinforcement Learning},
      author={Luke Rowe and Roger Girgis and Anthony Gosselin and Bruno Carrez and Florian Golemo  and Felix Heide and Liam Paull and Christopher Pal},
      journal={arXiv},
      volume={2403.19918},
      year={2024}
    }
