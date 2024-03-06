<script>
  import { page } from "$app/stores";
</script>


<nav class="primary-nav navbar navbar-expand-lg sticky-top">
    <div class="primary-container container-fluid">
        <a class="navbar-brand" href="/">
          <img src="../src/imgs/2473_logo.png" height="105" alt="">
        </a>

        <div class="collapse navbar-collapse" id="navbarNav">

          <ul class="navbar-nav me-auto">

            <li class="nav-item nav-button">
              <a class="nav-link nav-text" aria-current="page" target="_blank" href="https://github.com/beranki/pit-display">
                <i style="color: #fec42d;" class="fa fa-github"></i>
              </a>
            </li>

            <li class="nav-item nav-button">
              <a class="nav-link nav-text" aria-current="page" target="_blank" href="mailto: cupertinorobotics@gmail.com">
                <i style="color: #fec42d;" class="fa fa-envelope"></i>
              </a>
            </li>
            

            <li class="nav-item nav-button">
              <a class="nav-link nav-text" aria-current="page"  target="_blank" href="https://www.instagram.com/cupertinorobotics/">
                <i style="color: #fec42d;" class="fa fa-instagram"></i>
              </a>
            </li>

            <li class="nav-item nav-button">
              <a class="nav-link nav-text" aria-current="page" target="_blank" href="https://www.cupertinorobotics.org/">
                <i style="color: #fec42d;" class="fa fa-photo"></i>
              </a>
            </li>

            <li class="nav-item nav-button">
              <a class="nav-link nav-text" aria-current="page"  target="_blank" href="https://www.youtube.com/@CupertinoRoboticsCHS/featured">
                <i style="color: #fec42d;" class="fa fa-youtube"></i>
              </a>
            </li>

          </ul>

        </div>

        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav ms-auto">
              <li class="nav-item nav-link">
                <a style="color: #fec42d;" class="nav-link nav-text" aria-current="page" href="/">
                  home
                </a>
              </li>

              <li class="nav-item nav-link">
                <a style="color: #fec42d;" class="j nav-link nav-text" aria-current="page" href="/svr">
                  svr
                </a>
              </li>

              <li class="nav-item nav-link">
                <a style="color: #fec42d; text-decoration:dashed" class="nav-link nav-text" aria-current="page" href="/">
                  mbr
                </a>
              </li>
            </ul>
     
        </div>
      </div>
</nav>

<slot />