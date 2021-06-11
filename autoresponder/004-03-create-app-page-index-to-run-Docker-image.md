## Creating app page index.html to run Docker image

### TL;DR

The app page `index.html` allows the user to load variables from a JSON file, ie. `youtube-dl.json`, to customise each variable and to generate a custom Docker run command.

![Example Usage](https://github.com/dennislwm/docker-youtube-dl/blob/master/demo.gif)

### Creating an app page index.html

1. Create a file `index.html` and open it in your editor. Copy and paste the following code.

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <!-- Required meta tags always come first -->
  <meta charset="utf-8">

  <title>docker-youtube-dl</title>
  <div class="container">
    <!-- page body //-->
    <!-- One card per Step snippet -->
    <div class="row m-t-2">
      <div class="col-md-10">
        <div class="card">
          <div class="card-block">
            <h1>docker-youtube-dl</h1>
            <h3>Configuration:</h3>
            <div>Before building the Docker image using <b>docker build</b>, you can HARDCODE any configuration of
              youtube-dl by placing any supported command line option in the configuration file, <i>youtube-dl.conf</i>.
            </div><br>
            <h3>Variable:</h3>
            <div>Before running the Docker image using <b>docker run</b>, follow the steps BELOW, to load variables from
              a JSON file, <i>youtube-dl.json</i>, to customise variables, to save variables in JSON file, and
              finally to run the Docker image.</div><br>
            <h3>Alternative Usage:</h3>
            <div>$ docker run --rm youtube-dl --param1</div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- Bootstrap CSS first, then Mytheme CSS -->
  <!-- Bootswatch theme at https://www.bootstrapcdn.com/bootswatch/ -->
  <link href="https://stackpath.bootstrapcdn.com/bootswatch/4.4.1/lumen/bootstrap.min.css" rel="stylesheet">
  <link href="https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css" rel="stylesheet">
  <link rel="stylesheet" href="css/mytheme.css">
</head>

<body>
  <div class="container">
    <!-- page body //-->
    <!-- One card per Step snippet -->
    <div class="row m-t-2">
      <div class="col-md-10">
        <div class="card">
          <div class="card-block">
            <h2>Step 1: Load Dynamic Variables</h2>
            <div>Example: <i>youtube-dl.json</i></div>
            <!-- btnLoadTable to Load JSON Table -->
            <button type="button" id="btnLoadTable">Load JSON File</button>
            <input id="finLoadTable" type="file" onchange="handleFiles(this.files)" style="display: none;" />

            <!-- divTable to Display JSON Table -->
            <div id="divTable"></div>
          </div>
        </div>
      </div>
    </div>
    <!-- End of One card per Step snippet -->
    <!-- One card per Step snippet -->
    <div class="row m-t-2">
      <div class="col-md-10">
        <div class="card ">
          <div class="card-block">
            <h2>Step 2: Enter User Parameter</h2>
            <table>
              <tr>
                <th>Date After (Inclusive):</th>
                <th>
                  <input type="text" id="txtUser1" value="Date" oninput="outUser1.value = txtUser1.value"></th>
              </tr>
              <tr>
                <th>Max Download:</th>
                <th>
                  <input type="range" id="txtUser2" value="10" min="0" max="10"
                    oninput="outUser2.value = txtUser2.value">
                  <output id="outUser2">10</output></th>
              </tr>
              <tr>
                <th>Playlist URL:</th>
                <th>
                  <input type="text" id="txtUser3" value="URL" oninput="outUser3.value = txtUser3.value"></th>
              </tr>
              <tr>
                <th>Path:</th>
                <th>
                  <input type="text" id="txtUser4" value="path" oninput="outUser4.value = txtUser4.value"></th>
              </tr>
            </table><br>
            <button type="submit" id="btnSubmit">Generate</button>
          </div>
        </div>
      </div>
    </div>
    <!-- End of One card per Step snippet -->
    <!-- One card per Step snippet -->
    <div class="row m-t-2">
      <div class="col-md-10">
        <div class="card ">
          <div class="card-block">
            <!-- divResult to Display Random Generated Password -->
            <h2>Step 3: Result</h2>
            <div id="divResult"></div>
          </div>
        </div>
      </div>
    </div>
    <!-- End of One card per Step snippet -->
  </div>
  <!-- footer //-->
  <div class="container footer">
    <div class="row">
      <div class="col-lg-12">
        &copy; Copyright 2019 Dennis Lee
      </div>
    </div>
  </div>

  <!-- jQuery first, then Bootstrap JS. -->
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
  <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"
    integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM"
    crossorigin="anonymous"></script>
  <!-- Click event of btnLoadTable -->
  <script>
    //--- Assert global variables
    var objUserTable = {};  // store User Custom Table Mapping, e.g. {a:"@",t:"+"}
    $(document).ready(function () {
      //--- Assert btnLoadTable click event triggers the open file dialog
      $('#btnLoadTable').click(function () {
        $('#finLoadTable').trigger('click');
      })
    })
    //--- Assert function to load local JSON file into the global variable objUserTable as an array of { key: "value" } pair
    //    Assert displays objUserTable in HTML
    function handleFiles(file) {
      //  console.log(file[0].name);
      //--- Assert load local JSON file using ajax GET
      $.ajax({
        url: file[0].name,
        success: function (data) {
          //  console.log(data);
          //--- Assert display table in divTable
          var strHtml = "";
          var intSize = 0;
          var strUser = "";
          $.each(data, function (i, objRow) {
            //  console.log(i, " ", objRow);
            intSize++;
            strUser = "#txtUser" + intSize;
            //  
            //  assign json variables to HTML elements
            $(strUser).val(objRow);
          })
          strHtml = "<h4>Loaded " + intSize + " variables";
          $('#divTable').html(strHtml);
        },
        error: function (err) {
          alert(err.responseText);
        }
      })
    }
    $(document).ready(function () {
      //--- Assert function to generate random passwords when User clicks on Button
      $('#btnSubmit').click(function () {
        var strDocker = "docker run --name objYouTube --rm ";
        var strYouTube = "";
        if ($('#txtUser1').val().length > 0)
          strYouTube += "--dateafter " + $('#txtUser1').val() + " ";
        if ($('#txtUser2').val().length > 0)
          strYouTube += "--max-downloads " + $('#txtUser2').val() + " ";
        if ($('#txtUser3').val().length > 0)
          strYouTube += $('#txtUser3').val() + " ";
        if ($('#txtUser4').val().length > 0)
          strDocker += "-v " + $('#txtUser4').val() + ":/media:rw ";
        const strHtml = strDocker + "youtube-dl " + strYouTube;
        $('#divResult').html(strHtml);
      })
    })
  </script>
</body>

</html>
```

2. Create a file `css/mytheme.css` and open it in your editor. Copy and paste the following code.

```css
/* Footer flushed at bottom of page */
html {
  position: relative;
  min-height: 100%;
}
body {
  margin-bottom: 60px; /* Margin bottom by footer height */
}
.footer {
  position: absolute;
  bottom: 0;
  height: 60px; /* Set the fixed height of the footer here */
  line-height: 60px; /* Vertically center the text there */
}
/* End of Footer */
/* Cards */
.card {
  border: none /* Remove border around cards */
}
.card-block {
  padding-bottom: 50px; /* Padding between cards */
}
/* End of Cards */
/* Code */
pre {
  background: #f4f4f4;
  border: 1px solid #ddd;
  border-left: 3px solid #f36d33;
  color: #666;
  page-break-inside: avoid;
  font-family: monospace;
  font-size: 15px;
  line-height: 1.6;
  margin-bottom: 1.6em;
  max-width: 100%;
  overflow: auto;
  padding: 1em 1.5em;
  display: block;
  word-wrap: break-word;
}
/* End of Code */
```

3. Open the file `youtube-dl.conf`.

* insert option `--ignore-error` (prevent errors caused by private videos)
* remove option `--max-downloads` (passing this as a dynamic parameter to **youtube-dl**)
* insert option `--verbose` (print various debugging information)

```yaml
# ... previous option --dateafter

# Ignore errors caused by private videos, etc
--ignore-errors

# ... previous option -o output filename

# Abort after downloading NUMBER files
# --max-downloads 10

# ... previous option --restrict-filenames

# Print various debugging information
--verbose
```

4. Open the file `Dockerfile`.

* modify directive `LABEL` (change name and email)
* add directive `CMD` (running this container without passing any parameters prints the **youtube-dl** version)

```docker
# ... previous directive FROM

LABEL maintainer="Dennis Lee <dennislwm@gmail.com>"

# ... previous directive ENTRYPOINT

CMD ["--version"]
```

5. Create a file `youtube-dl.json` and open it in your editor. Copy and paste the following code.

```json
{
  "dateafter": "20200709",
  "max-downloads": "10",
  "url": "https://www.youtube.com/playlist?list=PLuK5ZVXOaYHRkonVMKe66LF7eVbFcXcgV",
  "workpath": "d:/docker/youtube-dl/"
}
```

Update: Add a key-pair `datebefore` to the above JSON file. The reason is if there are more than `max-downloads` files available, **youtube-dl** downloads the files in descending order from latest to earliest date.
