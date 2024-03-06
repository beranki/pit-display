<script>
    import { afterUpdate, onMount } from 'svelte';
    import { page, navigating } from '$app/stores';
    import { goto } from '$app/navigation';

    import * as THREE from 'three';

    import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
    import { STLLoader } from 'three/addons/loaders/STLLoader.js';
    import { Mesh } from 'three/src/objects/Mesh.js'

    let container, stats, cad_file_path;
    let requestId;
    let controls; 
    let camera, camTarget, scene, renderer;
    let geo_ref;

    $: current_model = "total";
    $: stl_loaded = false;

    cad_file_path = "/src/cads/svr/total.stl";


    function STLViewer(model, elementID) {

        console.log("AA")

        var elem = document.getElementById(elementID);

        camera = new THREE.PerspectiveCamera(36,
            elem.clientWidth/elem.clientHeight, 1, 500)

        camera.position.x = -2;
        camera.zoom = 10

        renderer.setSize(elem.clientWidth, elem.clientHeight);
        elem.appendChild(renderer.domElement);

        window.addEventListener('resize', function () {
            renderer.setSize(elem.clientWidth, elem.clientHeight);
            camera.aspect = elem.clientWidth/elem.clientHeight;
            camera.updateProjectionMatrix();
        }, false);

        controls = new OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true;
        controls.rotateSpeed = 0.05;
        controls.dampingFactor = 0.1;
        controls.enableZoom = true;
        controls.autoRotate = true;
        controls.autoRotateSpeed = .75;
        
        (new STLLoader()).load(model, function (geometry) {
            geo_ref = geometry;
            var material = new THREE.MeshPhongMaterial({ 
                specular: 100, 
                shininess: 100 });
            var mesh = new Mesh(geometry, material);
                scene.add(mesh);
            
            material.color = new THREE.Color("rgb(238, 238, 238)")

            var middle = new THREE.Vector3();
            geometry.computeBoundingBox();
            geometry.boundingBox.getCenter(middle);

            mesh.geometry.applyMatrix4(new THREE.Matrix4().makeTranslation( 
                                        -middle.x, -middle.y, -middle.z ) );

            mesh.rotation.x = Math.PI/2;
            mesh.rotation.y = Math.PI;
        
        var largestDimension = Math.max(geometry.boundingBox.max.x,
                            geometry.boundingBox.max.y, 
                            geometry.boundingBox.max.z)
        camera.position.z = largestDimension * 1.5;
        
        console.log("REACHED")

        animate();

    });
  }

  var animate = function () {
      requestId = requestAnimationFrame(animate);
      controls.update();
      renderer.render(scene, camera);
      var axis = new THREE.Vector3(0, 0.5, 0)
  }; 

  var animStop = function() {
    console.log("AAss")
    window.cancelAnimationFrame(requestId);
    requestId = undefined;
    renderer.clear();
    console.log(scene.children);
    clearScene(scene);
  }

  var toggleCADs = function(model) {
    animStop();
    stl_loaded = false;

    STLViewer(model, "model");
    
    stl_loaded = true;

    console.log(model);

    if (model == "/src/cads/svr/chain_mech.stl") {
      current_model = "chain";
    } else if (model == "/src/cads/svr/shooter.stl") {
      current_model = "shooter";
    } else if (model == "/src/cads/svr/total.stl") {
      current_model = "total";
    } else if (model == "/src/cads/svr/chassis.stl") {
      current_model = "chassis";
    }

  }

  function clearScene(scene) {
    for (let i = scene.children.length - 1; i >= 0; i--) {
      const object = scene.children[i];
      if (object.type === 'Mesh') {
        object.geometry.dispose();
        object.material.dispose();
        scene.remove(object);
      }
    }
  }

  $: console.log($page.route.id)
   $: if ($navigating && $navigating.from.url.pathname == '/svr') {
    console.log("CLEARING ALL")
    animStop()
   }

  onMount(async () => {
      stl_loaded = false;
      if (scene == null) {
        scene = new THREE.Scene();
        scene.add(new THREE.HemisphereLight(0xffffff, 1.5));    
        scene.background = new THREE.Color("rgb(51, 54, 70)");
        renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
      }

      console.log("mounted")
      STLViewer(cad_file_path, "model");
      stl_loaded = true;
  })
</script>


<div class = "svr-container">
      <div class = "vstack gap-3 cad-browser">
        <div class="dropdown-div">
          <ul class="tree">
              <li>
                <details open>
                  <summary>cads</summary>
                  <ul>
                    <li>
                      <details>
                        <summary>svr</summary>
                        <ul>
                          <li on:click={() => {
                            if (current_model != "total") {
                              toggleCADs("/src/cads/svr/total.stl")
                            }
                          }}>
                            total
                          </li>

                          <li on:click={() => {
                            if (current_model != "shooter") {
                              toggleCADs("/src/cads/svr/shooter.stl")
                            }
                          }}>
                            shooter
                          </li>

                          <li on:click = {() => {
                            if (current_model != "chain") {
                              toggleCADs("/src/cads/svr/chain_mech.stl")
                            }
                            }}>
                            chain
                          </li>

                          <li on:click = {() => {
                            if (current_model != "chassis") {
                              toggleCADs("/src/cads/svr/chassis.stl")
                            }
                            }}>
                            chassis
                          </li>
                        </ul>
                      </details>
                    </li>
                    <li>
                      <details>
                        <summary>mbr</summary>
                        <ul>
                          <li>work</li>
                          <li>in</li>
                          <li>progress!</li>
                        </ul>
                      </details>
                    </li>
                  </ul>
                </details>
              </li>
            </ul>
        </div>

        <div class="cad-notif-section">
          zoom and drag these cads! 🠮
        </div>

      </div>

      <div class = "cad-preview">
          <div id="model" style="width: 50vw; height: 85vh"></div>
      </div>

      <div class = "info-blurb">
          <div id="expl-tags" style="width: 40vw; height: 85vh"> 
            {#if current_model == "chain"}
            <div>
              <div class="vstack gap-3">
                  <div class = "heading">
                      chain mech
                  </div>
              
                  <div class = "text-blurb-div">
                    <h4 style="text-decoration: underline">Summary</h4>
                    Lifts the robot through chain/sprocket and hooks onto the chain. <br>
                    There is also a winch system that prevents the system from <br>
                    going back up after the hook has moved down. <br>
                    <br>

                    <h4 style="text-decoration: underline">Match Functionality</h4>
                    During the endgame, the driver should align the center of the <br>
                    robot with the lowest point of the chain using a second driver camera. <br>
                    When ready, they should hold a button in order to bring the mechanism <br>
                    down. The driver should release after the hook has successfully lifted <br>
                    the robot off the ground using the camera to view whether or not the robot <br>
                    is completely off the ground. The limit switch should stop the hook from <br>
                    moving further down at its maximum extension point in order to prevent <br>
                    the mech from overextending. <br>
                    <br>

                    <h4 style="text-decoration: underline">Driver Station Functionality</h4>
                    The hooks only move down. They move while the driver is <br>
                     holding the push button and upon release, the hooks stop moving. <br>
                    <br>
                  </div>
                </div>
            </div>
            {:else if current_model == "shooter"}
            <div>
              <div class="vstack gap-3">
                  <div class = "heading">
                      shooter
                  </div>
              
                  <div class = "text-blurb-div">
                    <h4 style="text-decoration: underline">Summary</h4>
                    Run two NEO 1650’s at a ~55 degree angle and at different speeds <br>
                    to intake from source and shoot at speaker, with a limit switch to <br>
                    detect a note. <br>
                    <br>

                    <h4 style="text-decoration: underline">Match Functionality</h4>
                    When aligned to the source, upon input, the motor begins <br>
                    spinning in intake direction (speed 0.4 on WPILib motor interface) <br>
                    to ingest the note. Once the limit switch triggers, turn off both motors. <br>
                    <br>

                    When aligned to the speaker, upon input, the motor begins spinning <br>
                    in the outtake direction at max speed (1.0 on WPILib motor interface). <br>
                    The lower motor spins slower than the top motor. The lower motor “feeds” <br>
                    the note to the top motor to “shoot.” This is copied from the kitbot, <br>
                    details may be found on the kitbot’s page. <br>
                    <br>
                    
                    <h4 style="text-decoration: underline">Driver Station Functionality</h4>
                    Can intake note (motors spinning inwards) with one button. Both motors <br>
                    spin at the same time. Press the left bumper to rev up top shooter motor, <br>
                    and after a few seconds, press triangle to trigger bottom shooter motor <br>
                    and shoot out note. <br>

                  </div>
              </div>
          </div>
          {:else if current_model == "chassis"}
          <div>
              <div class="vstack gap-3">
                <div class = "heading">
                    chassis
                </div>
              
                <div class = "text-blurb-div">
                    Our robot is a 26 inch chassis with 4 swerve drive modules.
                    <br>
                    <br>

                    <h4 style="text-decoration: underline">Electrical Chassis Plot ↴</h4>
                    <img src="../src/imgs/electrical_chassis_layout.png" alt="Electrical Chassis Layout" />
                </div>
              </div>
          </div>

          {:else}
              <div>
                <div class="vstack gap-3">
                    <div class = "heading">
                        svr mech
                    </div>
                
                    <div class = "text-blurb-div">
                        <strong>DUSTY-24A</strong>: The culmination of the chassis, shooter mech, and chain <br>
                        mech. Our strategy revolves around a simple and consistent robot for our <br>
                        first tournament, SVR. For both tournaments, our robot will contain a <br>
                        shooter mechanism utilizing spinning wheels that can score notes in <br>
                        the speaker. Additionally, we will use a climber mech that allows us <br>
                        to latch onto the chain and lift the robot completely off the ground. <br>
                        <br>

                        These mechanisms are powered by code written by the SW-Mech, <br> 
                        SW-Automation, and SW-Drive subteams: <br>
                        <ul>
                          <li>
                            SW-Automation wrote code for pickup and scoring alignment, detecting <br>
                            AprilTags using the Pupil Apriltags library (with ~2" accuracy on <br>
                            pose estimations). They did so via a brand new implemented Raspberry <br>
                            Pi system, learning protocols like SCP and SSH to set up NetworkTables. <br>
                            They used LEDs to help drivers visually. <br>
                          </li>
                          
                          <li>
                            SW-Mech wrote code for the mechanisms through the creation of FSMs <br>
                            for chain and shooter. This code triggers different states through various <br>
                            button presses on the programmed "Mech" PS4 controller. <br>
                          </li>
                          

                          <li>
                            SW-Drive helped program <a href="/svr/auto_paths">autonomous paths</a> using complex <br>
                            Swerve Module path planning pipelines, and the agile and responsive <br>
                            movement of our Swerve Modules that allows us to dart across the <br>
                            field with precision. SW-Drive worked with Automation to help optimize <br>
                            alignment to AprilTags during TeleOp. <br>
                          </li>
                        </ul>

                    </div>
                </div>
            </div>
            {/if}

          </div>
      </div>

</div>
