<script>
    $: current_path = 1;
</script>


<div class = "svr-container">
    <div class = "vstack gap-3 path-browser">
      <div class="dropdown-div">
        <ul class="tree">
            <li>
              <details open>
                <summary>auto paths</summary>
                <ul>
                  <li on:click={() => {
                    current_path = 1
                  }}>
                    path 1
                  </li>
                  <li on:click={()=>{
                    current_path = 2
                  }}>
                    path 2
                  </li>
                  <li on:click={() => {
                    current_path = 3
                  }}>
                    path 3
                  </li>
                </ul>
              </details>
            </li>
          </ul>
      </div>

      <div class="path-notif-section">
       <!--     <a class="redirect-button" href="/svr/"> return to svr </a> -->
      </div>

    </div>


    <div class = "vstack gap-0">
        <div class = "auto-path-title">
            {#if current_path == 1}
                Auto Path 1 - Shoot in the Center and Leave
            {:else if current_path == 2}
                Auto Path 2 - Shoot on the Source Side and Leave
            {:else if current_path == 3}
                Auto Path 3 - Shoot on the Amp Side and Leave
            {/if}
        </div>

        <div class="path-image-wrapper">
            {#if current_path == 1}
                <img src="../src/imgs/auto_paths/path1.png" alt = "auto path 1" style="width: 70vw;" />
            {:else if current_path == 2}
                <img src="../src/imgs/auto_paths/path2.png" alt = "auto path 2" style="width: 70vw;" />
            {:else if current_path == 3}
                <img src="../src/imgs/auto_paths/path3.png" alt = "auto path 3" style="width: 70vw;" />
            {/if}
        </div>
    </div>

</div>
