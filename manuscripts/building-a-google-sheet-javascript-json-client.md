- Building a Google Sheet Javascript JSON Client
- Step 1 - Setting Up Google Sheet
  
  (1) In your web browser, navigate to Google Drive and create a new spreadsheet.
  
  (2) In the first row, enter the headers for the first FOUR (4) columns:
- First, Last, Company, Group
  
  (3) In the next two rows, enter some dummy records:
- Laurence, Smith, Discovery, 7
- John, Svekis, ACME, 6
  
  (4) Next, we click on menu **Files --> Publish to the Web**
- Select tab **Link**, and choose **Sheet1**, **Web page**
- Click on **Publish** button
  
  (5) Copy and paste the link into a new browser tab:
  
     https://docs.google.com/spreadsheets/d/e/2PACX-1vQTlJD50wdWnCB7IVGMCd1pIgnxKaD83qdz34khW73HmBu459kIxHkl8EMXH0HUNQqclx_2YRQ1BWvG/pubhtml?gid=0&single=true 
  
  The Google Sheet token is the long string between *spreadsheets/* and */edit*:
  
     1iawua0sFYWI_IoRucg2g0tXDz1VBTlfN0DH6FXKWPRI
  
  To view the sheets data in JSON format, we use the following HTTP URL:
  
     https://spreadsheets.google.com/feeds/list/<token>/od6/public/values?alt=json 
  
  where <token> is replaced with the above string. For example:
  
     https://spreadsheets.google.com/feeds/list/1iawua0sFYWI_IoRucg2g0tXDz1VBTlfN0DH6FXKWPRI/od6/public/values?alt=json 
  
  
  
  ![][1]
  
  [1]: images/building-a-google-sheet-javascript-json-client/step-1---setting-up-google-sheet.png
- Step 2 - Creating a New Index.html File
  
  (1) Make a new folder sheetapi/ ["root"]
  
  (2) In your root folder, create a new file index.html and enter the following code:
  
     <!DOCTYPE html> 
     <html lang="en"> 
       <head> 
         <!-- Required meta tags always come first --> 
         <meta charset="utf-8"> 
         <title>Google Sheet JSON Client</title> 
       </head> 
       <body> 
         <h1>Building a Google Sheet Javascript JSON Client</h1>
         <!-- jQuery btnLoadJson to Load JSON --> 
         <button type="button" id="btnLoadJSON">Load JSON</button>
         <!-- jQuery divOutput to Display JSON --> 
         <div id="divOutput"></div>
         
         <!-- jQuery first, then Bootstrap JS. --> 
         <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script> 
         <!-- jQuery function to Load JSON for btnLoadJson --> 
         <script>
           $(function() {
             $('#btnLoadJSON').click(function(){
               var strUrl="https://spreadsheets.google.com/feeds/list/1iawua0sFYWI_IoRucg2g0tXDz1VBTlfN0DH6FXKWPRI/od6/public/values?alt=json";
               $.getJSON(strUrl, function(jsnData){
                 console.log(jsnData);
               })
             })
           })
         </script>
       </body> 
     </html> 
  
  (3) Create a button **btnLoadJson** to load the JSON data.
  
  (4) Create an output **divOutput** to display the JSON data.
  
  (5) Include the JQuery script file.
  
  (6) Make a new function to display the JSON data in the console log, when the user clicks on the **btnLoadJson**, by calling **getJSON()**.
- Step 3 - Peeking at the JSON Data Using Chrome Developer
  
  (1) In your web browser, open the **index.html** file.
  
  (2) Click **Load JSON** button.
  
  (3) Right-click anywhere on the Index page, and select menu item **Inspect** menu.
  
  (4) Click on **Console** tab, which should display an **Object**. Expand the **Object**:
- **feed** is the branch of **Object** that contains the contents of the sheet
  
  (5) Expand **feed** to display several objects (we will ignore the other objects for now):
- **entry: Array() **- this an array that keeps the contents of the cell
- **title** - this is the name of the sheet, e.g. "Sheet1"
  
  (6) Expand **entry[0]** to display more objects. The cells that contain the data is located in **gsx$<header>**. There should be one per column, where **<header>** is the title of the column (we will ignore the other objects for now):
- **gsx$<header>** - content of cell corresponding to the **<header>** column
  
  ![][2]
  
  [2]: images/building-a-google-sheet-javascript-json-client/step-3---peeking-at-the-json-data-using-chrome-developer.png
- Step 4 - Client getJSON
  
  (1) Since we're interested in the contents of the cells, i.e. **entry** array, find the **getJSON** code between { }:
  
                 console.log(jsnData);
  
  and replace with the following code:
  
     { 
                 console.log(jsnData.feed.entry);
                 var strHtml="<h3>Google Data Output</h3>"
                 $.each( jsnData.feed.entry, function(idx,objRow) {
                   console.log(objRow);
                   strHtml += "<div>" + objRow.gsx$first.$t;
                   strHtml += " " + objRow.gsx$last.$t;
                   strHtml += " " + objRow.gsx$company.$t;
                   strHtml += " " + objRow.gsx$group.$t;
                   strHtml += "</div>";
                 })
                 $('#divOutput').html(strHthml);
     }
  
  We narrow the display in the console log, by specifing the sub-object **jsnData.feed.entry**.
  
  As the entry object is an array, we can use the function **$.each()** to iterate through each entry, where **idx** is the index of the entry, and **objRow** contains the row.
  
  We narrow further the display in the Html, by specifing the sub-object** objRow.gsx$first.$t**.
  
  For each column in the spreadsheet, we have the corresponding sub-object **objRow.gsx$<header>.$t**.
  
  Warning: DO NOT use hyphen ["-"] in the header, as Javascript will throw an error when accessing the sub-object. 
  
  Finally, we output the Html, by calling the **html()** method of the selector **$('#divOutput')**. The selector allows us to use any object that we have tagged using the **id** tag, e.g. **<div id="divOutput">**.
- Step 5 - Google App Script
  
  (1) In your Google spreadsheet, click on the menu **Tools-->Script editor**. This allows you to write server side Javascript code in the Google App Script tool.
  
  (2) Type in the following code:
  
     function doGet(e) {
       try {
         var ss = SpreadsheetApp.openById("<token>");
         var sheet = ss.getSheetByName('Sheet1');
         var data = sheet.GetRange(2, 1, sheet.getLastRow()-1, 6).getValues();
         var jsonData = JSON.stringify(data);
         return ContentService.createTextOutput(jsonData).setMimeType(ContentService.MimeType.JSON);
       }
       catch(e) {
         var error = { "error":e }
         var jsonError = JSON.stringify(error);
         return ContentService.createTextOutput(jsonError).setMimeType(ContentService.MimeType.JSON);
       }
     }
  
  Replace **<token>** with your Google sheet token.
  
  ![][3]
  
  [3]: images/building-a-google-sheet-javascript-json-client/step-5---google-app-script.png
- Step 6 - Server doGet
  
  (1) Now that we have tested our Google App script **sheetapi**, let's fetch the JSON data from our client.
  
  (2) Find the Button selector **$('#btnLoadJSON').click(function(){})**, and replace the code within it as follows:
  
               var strUrl="https://script.google.com/macros/s/AKfycby6MaFKuHx5glRIrePN1nB72mXCTTwDPfhlLdZvdHaBrCKRq-6E/exec";
               $.getJSON(strUrl, function(jsnData){
                 console.log(jsnData);
                 var strHtml="<h3>Google Data Output</h3><ul>";
                 $.each(jsnData, function(idx,objRow){
                   strHtml += "<li>";
                   for(var x=0; x<objRow.length; x++) {
                     strHtml += objRow[x] + " ";
                   }
                   strHtml += "</li>";
                 })
                 strHtml += "</ul>";
                 $('#divOutput').html(strHtml); 
               })
  
  First, we have substituted with the published Web App URL in the **strUrl** variable.
  
     strURL = https://script.google.com/macros/s/AKfycby6MaFKuHx5glRIrePN1nB72mXCTTwDPfhlLdZvdHaBrCKRq-6E/exec 
  
  Second, the top node of **jsnData** is no longer **feed**, but just the array. 
  
  Hence, we iterate through the array using variable **x**, and also use **objRow.length** to include all elements in the array.
  
  
  
  ![][4]
  
  [4]: images/building-a-google-sheet-javascript-json-client/step-6---server-doget.png
- Step 7 - Server doPost
  
  (1) In your Google spreadsheet, click on the menu **Tools-->Script editor**. 
  
  (2) Append the following code before the **doGet()** function:
  
     function doPost(e) {
       try {
         var ss = SpreadsheetApp.openById("1iawua0sFYWI_IoRucg2g0tXDz1VBTlfN0DH6FXKWPRI");
         var sheet = ss.getSheetByName('Executive');
         var header = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0];
         var holderArray = [];
         for(var x=0; x<header.length; x++) {
         // assert column name exists in header
           var tempValue = !e.parameter[header[x]] ? '' : e.parameter[header[x]];
           holderArray.push(tempValue);
         }
         sheet.appendRow(holderArray);
         var result = {"data":e.parameter, "holder":holderArray};
         var jsonData = JSON.stringify(result);
         return ContentService.createTextOutput(jsonData).setMimeType(ContentService.MimeType.JSON);
       }
       catch(e) {
         var error = { "error":e }
         var jsonError = JSON.stringify(error);
         return ContentService.createTextOutput(jsonError).setMimeType(ContentService.MimeType.JSON);
       }
     }
  
  First, we check for each column name in header, that **e.parameter** exists, then we push the value of **e.parameter** into a **holderArray**. Otherwise, we push an empty string.
  
  This is to prevent the user from posting values that does not have a valid column name in our spreadsheet.
  
  Second, we use **sheet.appendRow()** method to append the values of **holderArray **into our spreadsheet.
  
  Optionally, we return the result of holderArray as JSON format to the user.
- Step 8 - Form Setup to doPost
  
  (1) Open the **index.html** file, and find the button **Load JSON** code:
  
         <button type="button" id="btnLoadJSON">Load JSON</button>
  
  Type the following code after the </button>
  
     <form id="frmPost">
       <div>First Name: <input type="text" name="First" value="First Name"><br></div>
       <div>Last Name: <input type="text" name="Last" value="Last Name"><br></div>
       <div>Company: <input type="text" name="Company" value="Company"><br></div>
       <div>Group: <input type="text" name="Group" value="Group"><br></div>
       <div>Email: <input type="text" name="Email" value="first@email.com"><br></div>
       <button type="submit" id="btnSubmit">Send Data to Google Sheet</button>
     </form>
  
  Ensure that the names of each input tag matches the column names in the spreadsheet, i.e. "First"
  
  (2) In order to post the above form, we write code using jQuery.
  
  Type the following code between the <script> and </script> tags:
  
     $(function() {
       $('#frmPost').submit( function(e) {
         e.preventDefault();
         var myData = $('form#myForm :input').serialize();
         var strUrl = "https://script.google.com/macros/s/AKfycby6MaFKuHx5glRIrePN1nB72mXCTTwDPfhlLdZvdHaBrCKRq-6E/exec";
         console.log(myData)
         $.ajax({
           url: strUrl,
           method: 'POST',
           data: myData,
           success: function(data) {
             console.log(data)
           }
         })
       })
     })
  
  First, the **strUrl** variable contains the same published Web App URL above.
  
  Second, we serialize the form inputs into a string using the form selector and its **serialize()** method.
  
  Finally, we call the **.ajax() **function and passing it the parameters **strUrl**, **"POST"**,** myData**, and a success **function()**, which will display the result.
  
  ![][5]
  
  [5]: images/building-a-google-sheet-javascript-json-client/step-8---form-setup-to-dopost.png