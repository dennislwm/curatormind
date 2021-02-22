# Creating a Free, Fast, Secure and Serverless Password Generator

**Introduction**

This project was created by me because I couldn't find a Password generator that had these features in combination:

* Using English names or dictionary words
* Custom table mapping of alphabets to special characters
* Password strength checker
* Free, fast, responsive, secure and serverless

Basically, I'm not very good at remembering passwords. Hence, I always use English names or dictionary words as passwords. 

This becomes an issue because any potential hackers can brute hack these passwords. However, if I were to replace some alphabets with special characters, such as a -> @, then the difficulty of hacking has gone up significantly, while my password can still be easily recalled from my memory.

In other words, so long as you keep secure the custom table of maps, then you should be able to thwart hackers from guessing your passwords, while keeping your mind sane from remembering all types of weird password combinations. 

The motivation for a free, fast, secure and serverless web application came from Hat.sh, where you could encrypt a file without uploading it to a server as everything is done offline in your browser.

**Responsive and Fast**

Basically, I used my own [blogstrapi](https://github.com/dennislwm/blogstrapi) boilerplate as a starting point for this web app. It is responsive and fast because it uses both vanilla Bootstrap 4 and jQuery.

**Secure and Serverless**

As I had to find a way to generate random names, using an existing HTTP API was the easiest. However, after fetching the names, this array of strings has to be transformed using a local JSON table into possible passwords.

The **data.JSON** is a file that resides on your local computer, and it doesn't get uploaded to a server, as the post-processing is done locally in your browser.

## Prerequisites

This project is built using **Bootstrap 4** and **jQuery**, and it has the following dependencies:

1. **randomuser.me** - HTTP API to generate random names

As this article assumes you have some knowledge on how to build a HTML site, I will not go through the basics. 

You can always check out my article on [Creating a Blog Theme with Bootstrap and Harp](http://tldr.pro/blog/blog/creating-a-blog-theme-with-bootstrap-and-harp.html) for the basics.

## Step 1 - Understanding the Custom Table of Maps

The key to this project is the custom table of maps, in a **data.json** file:

     {
         "a": "@",
         "s": "$",
         "t": "+"
     }

For example, the random name "Sebastian" will be transformed to password "Seb@$+i@n".

You will need to create your own **data.json** file, or copy the above code as a starting point. Also, note that the **data.json** file is case-sensitive, so "a" is not the same as "A".

You can map any alphabet to any character as you wish. These are some valid examples:

     "A": "@",
     "tt: "++"

The only restriction is that you cannot map from a longer string of characters to a shorter string of characters. For example, this is NOT permitted and will be ignored by the script:

     "the": "+",

The less standardize your metadata is, the better it will be for your passwords.

**Open File Dialog in HTML**

Creating an open file dialog window requires two HTML tags **button** and **input type="file"**. The button tag represents the button, while the input tag handles the open file dialog event.

We give each tag a unique id so that we can select them in Javascript. For example:

     <!-- btnLoadTable to Load JSON file -->
     <button type="button" id="btnLoadTable">Load JSON Table</button>
     <input id="finLoadTable" type="file" onchange="handleFiles(this.files)" style="display: none;" />

Next, we need to define both the click event of **btnLoadTable** and the function **handleFiles()**.

     //--- Assert btnLoadTable click event triggers the open file dialog
     $(document).ready(function() {
       $('#btnLoadTable').click(function() {
         $('#finLoadTable').trigger('click');
       })
     })

The pseudo code for the function **handleFiles()** is as follows:

(1) Load local JSON file using **$.ajax() **GET method.

(2) For each key-value pair in the JSON file, add it to a global array **objUserTable[]** if **length(value) >= length(key)**.

(3) Display key-value pairs within a HTML **table**, one pair per row, in the **div** tag **divTable**.

![][1]

[1]: images/creating-a-free--fast--secure-and-serverless-password-generator/step-1---understanding-the-custom-table-of-maps.png

## Step 2 - Form User Inputs with Bootstrap 4

The form has several user inputs that are used to process the random names into passwords:

1. Min Length (Default: 6) - Minimum length of random name
1. Suffix Digit (Default: 2) - Append zero or more random digits at the end of password
1. Replace Probability (Default: 50%) - The probability that any alphabet will be replaced by a custom character
1. Max Output (Default: 5) - Generate up to maximum number of passwords

Most of these inputs are self-explanatory, however there one input that require some explanation.

**Replace Probability**

Changing this input will change the probability of an alphabet being replaced by your custom table of maps.

For example, if this value is 100%, the name "Arietta" will always yield the password "Arie++@".

However, if this value is set lower at 50%, the same name "Arietta" may yield different results, such as "Arie++a". In this example, the last letter "a" wasn't replaced with "@".

You can change its value to 100%, in which case all the alphabets will be replaced.

**Input Slider in HTML**

Creating an input slider requires two HTML tags **input type="range"** and **output**. The input tag represents the slider, while the output tag shows the current value of the slider.

We give each tag a unique id so that we can select them in Javascript. For example:

     <div>Min Length:
       <input type="range" id="txtLength" value="6" min="1" max="10" oninput="outLength.value = txtLength.value">
       <output id="outLength">6</output><br>
     </div>

The input tag has an event handler **oninput**, with inline Javascript:

     outLength.value = txtLength.value

As we used a unique id **txtLength** for our input tag, we can access this in Javascript.

The other input tags are similar in nature and they can all be accessed in Javascript using their unique ids:

- **txtSuffix**

- **txtProbability**

- **txtCount**

![][2]

[2]: images/creating-a-free--fast--secure-and-serverless-password-generator/step-2---form-user-inputs-with-bootstrap-4.png

## Step 3 - Using Math Library in jQuery

We use the Math library of jQuery to create our own **intMathRandom()** function. This function returns an integer between min and max (inclusive):

     function intMathRandom(intMin, intMax) {
       intMin = Math.ceil(intMin); 
       intMax = Math.floor(intMax);  
       return Math.floor( Math.random()*(intMax-intMin+1) ) + intMin; 
     }

For example, if we want to return a random number between 0 to 9, we call the function **intMathRandom(0,9)**.

Note: The Math.random() function returns a double value between 0 to 1, hence we need to scale up this value.

Next, we create a function **strAssertName()** that returns an empty string if the name does not meet our requirement, otherwise it returns a generated password. 

The pseudo code for the function **strAssertName()** is as follows:

(1) If the **length(name)** < **txtLength.value**, return an empty string.

(2) For each key-value pair in the global array **objUserTable[]**, use the function **intMathRandom() **to roll a random chance of replacement between 0 and 100.

(3) If the random roll passes the **txtProbability.value**, then replace any key (**idx**) occuring within the name with the value (**objRow**).

(4) If the **txtSuffix.value > 0**, use the function **intMathRandom()** to roll a random number between 0 and 9. Repeat as many times as required.

     function strAssertName(strName) {
       var strReturn='';
       intUserLength = $('#txtLength').val();
       intUserSuffix = $('#txtSuffix').val();
       intUserProbty = $('#txtProbability').val();
     //--- Assert name has minimum User length
       if( strName.length < intUserLength) return strReturn;
       strReturn = strName;
     //--- Replace each character in name if exists in objUserTable with a chance of non-replacement
       $.each( objUserTable, function(idx,objRow) {
       //--- Assert chance of replacement is from 0% to 100%
         if( intMathRandom(0,100)<=intUserProbty ) {
           strReturn = strReturn.replace(idx, objRow);
         }
       })
     //--- Assert add User suffix digit(s)
       for( var i=0; i<intUserSuffix; i++ ) {
         strReturn += intMathRandom(0,9);
       }
       return strReturn;
     }

## Step 4 - Peeking into RandomUser HTTP API with Postman

Before diving into the results, let's take a peek at how the RandomUser HTTP API work with the Postman app.

We pass the following parameters to the HTTP API:

(1) inc=name,nat

(2) nat=au,ch,es,fi,fr,gb,ie,nz,us

(3) results=2

For example, 

     https://randomuser.me/api/?inc=name,nat&nat=au,ch,es,fi,fr,gb,ie,nz,us&results=2

Their website [Randomuser.me](https://randomuser.me) has a detailed documentation so I won't bother explaining the above parameters.

Let's take a look at the JSON results returned by the HTTP API. Copy and paste the above URL into Postman:

     {
         "results": [
             {
                 "name": {
                     "title": "Miss",
                     "first": "Abigail",
                     "last": "Carter"
                 },
                 "nat": "GB"
             },
             {
                 "name": {
                     "title": "Miss",
                     "first": "Marina",
                     "last": "Pastor"
                 },
                 "nat": "ES"
             }
         ],
         "info": {
             "seed": "3b32e85b783e7af1",
             "results": 2,
             "page": 1,
             "version": "1.3"
         }
     }

The content that we want is an array **results**, where each row contains the object **name**, which consists of **title**, **first** and **last**. For example, the value of **results[0].name.first** is "Abigail".

## Step 5 - Displaying Results

Finally, as we have created most of our functions, we need to define the click event of **btnSubmit**.

The pseudo code for the above is as follows:

(1) Request from RandomUser.me to generate random names using **$.ajax() **GET method.

(2) We pass the parameters **inc**, **nat** and **results**. The results value is taken from the **txtCount** field, but multiplied by a factor, i.e. we request more names than **txtCount**, as some of these names will be discarded in the process.

(3) For each row in array **results[]**, we call the function strAssertName() passing the name as parameter. If the function returns a non-empty string, we push the returned password into a local array **arrResult[]**.

(4) Display the generated passwords, one per line, in the **div** tag **divResult**.

![][3]

[3]: images/creating-a-free--fast--secure-and-serverless-password-generator/step-5---displaying-results.png

## Conclusion

In this tutorial, you used Bootstrap 4 and jQuery to create a web application that processes random names, using rules from a local JSON data file, into generated passwords. You learned to use the Math library in jQuery, to manage JSON data from a HTTP API request, and even to convert a web into a desktop application using Electron.js.

## Get the Source Code

You can download the above source code from GitHub repository [dennislwm/serverless-pwd-gen](https://github.com/dennislwm/serverless-pwd-gen).

## What to do Next

You can further extend your Bot in several meaningful ways:

1. Dictionary Words - Find a HTTP API to generate random dictionary words
1. Password Strength Meter - Indicate the strength of each password in the results
1. Desktop App - Convert this web application to a Desktop app using Electron.js

