# Extending the BlogTldr Theme

(A) Background

The source code for a Blog is based on the book "Complete Bootstrap: Responsive Web Development with Bootstrap 4" by Matt Lambert, Bass Jobsen, David Cochran, and Ian Whitley, December 2017 ["Lambert et al 2017"], Packt Online, URL: www.packtpub.com

Also, we used some of the material from Traversy Media's YouTube video "Build A CMS Admin Bootstrap Theme From Scratch", January 3, 2017 ["Traversy 2017"]. 

However, it lacks several basic features, including:

(i) no web page or GUI to access the shortened URLs

(ii) cannot modify the shortened URLs

(B) Motivation

The motivation comes from creating my own job board theme that copies all of the functionality of SimpleJobScript ["SJS"], written using Bootstrap and PHP, to enable adding new features in the future.

However, in this document we will be focused on creating a Blog Theme to duplicate the functionalities of Nibble Blog that is bundled together with SJS.

(C) Example

(D) History

## Risk-adjusted Wish List

(A) Deploy Node.js to Heroku

(B) Admin Login

(C) Install using DLHarp boilerplate

     harp init myproject --boilerplate dennislwm/blogstrapi

## Step 9: Adding Page Templates

(1) Update the "_data.json" file with the following code:

     {
        "_comment": "template-specific variables and settings; we'll set up one variable for each page template which will hold the name of the page.",
        "index": { 
           "pageTitle": "Home" 
        }, 
        "contact": { 
           "pageTitle": "Contact" 
        }, 
        "blog-post": { 
           "pageTitle": "Blog Post" 
        } 
     }

(2) Now that "_data.json" has been updated, we need to create the actual page template EJS files like we did with index. In your root folder, create two new files: "contact.ejs" and "blog-post.ejs". For now, both of our new pages will use the same "_layout.ejs" file as the index file.

(3) Open up the "contact.ejs" and enter the following code:

     <div class="container">
         <!-- page title //-->
             <div class="row m-t-3">
                 <div class="col-md-12">
                     <h1>Contact</h1>
             </div>
         </div>
         <!-- page body //-->
             <div class="row m-t-3">
                 <div class="col-md-12">
                     <p>Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Vestibulum tortor quam, feugiat vitae, ultricies eget, tempor sit amet, ante. Donec eu libero sit amet quam egestas semper. Aenean ultricies mi vitae est. Mauris placerat eleifend leo. Quisque sit amet est et sapien ullamcorper pharetra. Vestibulum erat wisi, condimentum sed, commodo vitae, ornare sit amet, wisi. Aenean fermentum, elit eget tincidunt condimentum, eros ipsum rutrum orci, sagittis tempus lacus enim ac dui. Donec non enim in turpis pulvinar facilisis. Ut felis. Praesent dapibus, neque id cursus faucibus, tortor neque egestas augue, eu vulputate magna eros eu erat. Aliquam erat volutpat. Nam dui mi, tincidunt quis, accumsan porttitor, facilisis luctus, metus</p>
             </div>
         </div>
         <%- partial("partial/_mailing-list") %> 
     </div>

(4) Open up the "blog-post.ejs" and enter the following code:

     <div class="container">
         <!-- page title //-->
         <div class="row m-t-2 m-b-2">
             <div class="col-mb-12">
                 <h1>Post Title</h1>
             </div>
         </div>
         <!-- feature //-->
         <div class="row">
             <div class="col-md-12">
                 <p>Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Vestibulum tortor quam, feugiat vitae, ultricies eget, tempor sit amet, ante. Donec eu libero sit amet quam egestas semper. Aenean ultricies mi vitae est. Mauris placerat eleifend leo. Quisque sit amet est et sapien ullamcorper pharetra. Vestibulum erat wisi, condimentum sed, commodo vitae, ornare sit amet, wisi. Aenean fermentum, elit eget tincidunt condimentum, eros ipsum rutrum orci, sagittis tempus lacus enim ac dui. Donec non enim in turpis pulvinar facilisis. Ut felis. Praesent dapibus, neque id cursus faucibus, tortor neque egestas augue, eu vulputate magna eros eu erat. Aliquam erat volutpat. Nam dui mi, tincidunt quis, accumsan porttitor, facilisis luctus, metus</p>
             </div>
         </div>
         <!-- page body //-->
         <div class="row m-t-2">
             <div class="col-md-8">
                 <p>Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Vestibulum tortor quam, feugiat vitae, ultricies eget, tempor sit amet, ante. Donec eu libero sit amet quam egestas semper. Aenean ultricies mi vitae est. Mauris placerat eleifend leo. Quisque sit amet est et sapien ullamcorper pharetra. Vestibulum erat wisi, condimentum sed, commodo vitae, ornare sit amet, wisi. Aenean fermentum, elit eget tincidunt condimentum, eros ipsum rutrum orci, sagittis tempus lacus enim ac dui. Donec non enim in turpis pulvinar facilisis. Ut felis. Praesent dapibus, neque id cursus faucibus, tortor neque egestas augue, eu vulputate magna eros eu erat. Aliquam erat volutpat. Nam dui mi, tincidunt quis, accumsan porttitor, facilisis luctus, metus</p>
             </div>
             <!-- sidebar //-->
             <div class="col-md-4 hidden-md-down">
                 <p>sidebar</p>
                 <p>Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Vestibulum tortor quam, feugiat vitae, ultricies eget, tempor sit amet, ante. Donec eu libero sit amet quam egestas semper. Aenean ultricies mi vitae est. Mauris placerat eleifend leo. Quisque sit amet est et sapien ullamcorper pharetra. Vestibulum erat wisi, condimentum sed, commodo vitae, ornare sit amet, wisi. Aenean fermentum, elit eget tincidunt condimentum, eros ipsum rutrum orci, sagittis tempus lacus enim ac dui. Donec non enim in turpis pulvinar facilisis. Ut felis. Praesent dapibus, neque id cursus faucibus, tortor neque egestas augue, eu vulputate magna eros eu erat. Aliquam erat volutpat. Nam dui mi, tincidunt quis, accumsan porttitor, facilisis luctus, metus</p>
             </div>
         </div>
         <%- partial("partial/_mailing-list") %> </div> 
     </div>

(5) In your "partial" folder, create a new file called "_mailing-list.ejs" and enter the following code into it:

     <div class="row m-t-3">
         <div class="col-md-12">
             <p>Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Vestibulum tortor quam, feugiat vitae, ultricies eget, tempor sit amet, ante. Donec eu libero sit amet quam egestas semper. Aenean ultricies mi vitae est. Mauris placerat eleifend leo. Quisque sit amet est et sapien ullamcorper pharetra. Vestibulum erat wisi, condimentum sed, commodo vitae, ornare sit amet, wisi. Aenean fermentum, elit eget tincidunt condimentum, eros ipsum rutrum orci, sagittis tempus lacus enim ac dui. Donec non enim in turpis pulvinar facilisis. Ut felis. Praesent dapibus, neque id cursus faucibus, tortor neque egestas augue, eu vulputate magna eros eu erat. Aliquam erat volutpat. Nam dui mi, tincidunt quis, accumsan porttitor, facilisis luctus, metus</p>
         </div>
     </div>

- If all went as planned, your blog post should look like this:

![][1]

[1]: images/extending-the-blogstrapi-theme/step-9--adding-page-templates.png

## Step 10: Update Contact Page

(1) In your root folder, open the file "contact.ejs" and find the body section of the page that is wrapped in "<div class="col-md-12">. Remove its filler text and replace with the following code:

     <form> 
       <fieldset class="form-group"> 
         <label>Email</label> 
         <input type="email" class="form-control" placeholder="Enter email"> 
         <small class="text-muted">We'll never share your email with anyone else.</small> 
       </fieldset> 
       <fieldset class="form-group"> 
         <label>Name</label> 
         <input type="text" class="form-control" placeholder="Name"> 
       </fieldset> 
       <fieldset class="form-group"> 
         <label>Message</label> 
         <textarea class="form-control" rows="3"></textarea> 
       </fieldset> 
       <button type="submit" class="btn btn-primary">Submit</button> 
     </form>

- form: every form needs to start with a <form> tag. However, no special classes are required on this tag.

- fieldset: In Bootstrap 4, each form group uses a <fieldset> tag instead of <div>. In our case, we have inserted a class of "form-group" on it for our single input. This <fieldset> tag will be repeated in all inputs.

- input: on this HTML tag, you need to add a class of "form-control", which is required for all inputs in Bootstrap. 

- small: this tag with a class of "text-muted" will render the text small and light grey. This is the pattern you should use if you want to include some help text with your form input.

- button: on this HTML tag, you will use the class "btn btn-primary", which is the main button (blue by default). There are a total of six different Bootstrap classes for buttons: (i) primary; (ii) secondary (white); (iii) success (green); (iv) info (light blue); (v) warning (yellow); (vi) danger (red).

![][2]

[2]: images/extending-the-blogstrapi-theme/step-10--update-contact-page.png

## Step 11: Update Main Page

(1) The design of our main page is going to rely heavily on the Card component of Bootstrap. In your root folder, open the file "index.ejs" and find the body of the page section with the following wrapper "<div class="col-md-8">". Replace its filler text with the following code:

     <div class="card"> 
       <img class="card-img-top img-fluid" src="img/image.jpg" alt="Card image cap"> 
       <div class="card-block"> 
         <h4 class="card-title">Post title</h4> 
         <p><small>Posted by <a href="#">Admin</a> on January 1, 2016 in <a href="#">Category</a></small></p> 
         <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p> 
         <a href="#" class="btn btn-primary">Read More</a> 
       </div> 
     </div> 
     <div class="card"> 
       <div class="card-block"> 
         <h4 class="card-title">Post title</h4> 
         <p><small>Posted by <a href="#">Admin</a> on January 1, 2016 in <a href="#">Category</a></small></p> 
         <p>Pellentesque habitant morbi tristique...</p> 
         <a href="#" class="btn btn-primary">Read More</a> 
       </div> 
     </div> 
     <div class="card"> 
       <img class="card-img-top img-fluid" src="img/image.jpg" alt="Card image cap"> 
       <div class="card-block"> 
         <h4 class="card-title">Post title <span class="label label-success">Updated</span></h4> 
         <p><small>Posted by <a href="#">Admin</a> on January 1, 2016 in <a href="#">Category</a></small></p> 
         <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p> 
         <a href="#" class="btn btn-primary">Read More</a> 
       </div> 
     </div> 
     <div class="card"> 
       <div class="card-block"> 
         <h4 class="card-title">Post title</h4> 
         <p><small>Posted by <a href="#">Admin</a> on January 1, 2016 in <a href="#">Category</a></small></p> 
         <p>Pellentesque habitant morbi tristique senectus...</p> 
         <a href="#" class="btn btn-primary">Read More</a> 
       </div> 
     </div>

- div: Any instance of the Card component of Bootstrap must use the HTML <div> tag. We have four card components to represent four blog posts on our main page.

- image: On this HTML tag, we've added a class "img-fluid" so that the image stretches to fill the width of the card.

- div: On this html tag, we've added the class "card-block" which will contain the actual content.

- h4: On this html tag, we've added a class "card-title" for the title.

- p: For each paragraph of text (excluding helper text), we insert the class "card-text".

(2) For our sidebar, we also use the Card component but in this case we use a different variation. In our partial folder, create a new file "_sidebar.ejs" and enter the following text:

     <div class="card card-block"> 
       <h5 class="card-title">Recent Posts</h5> 
       <div class="list-group"> 
         <button type="button" class="list-group-item">Cras justo odio</button> 
         <button type="button" class="list-group-item">Dapibus ac facilisis in</button> 
         <button type="button" class="list-group-item">Morbi leo risus</button> 
         <button type="button" class="list-group-item">Porta ac consectetur ac</button> 
         <button type="button" class="list-group-item">Vestibulum at eros</button> 
       </div> 
       <div class="m-t-1"><a href="#">View More</a></div> 
     </div> 
     <div class="card card-block"> 
       <h5 class="card-title">Archives</h5> 
       <div class="list-group"> 
         <button type="button" class="list-group-item">Cras justo odio</button> 
         <button type="button" class="list-group-item">Dapibus ac facilisis in</button> 
         <button type="button" class="list-group-item">Morbi leo risus</button> 
         <button type="button" class="list-group-item">Porta ac consectetur ac</button> 
         <button type="button" class="list-group-item">Vestibulum at eros</button> 
       </div> 
       <div class="m-t-1"><a href="#">View More</a></div> 
     </div> 
     <div class="card card-block"> 
       <h5 class="card-title">Categories</h5> 
       <div class="list-group"> 
         <button type="button" class="list-group-item">Cras justo odio</button> 
         <button type="button" class="list-group-item">Dapibus ac facilisis in</button> 
         <button type="button" class="list-group-item">Morbi leo risus</button> 
         <button type="button" class="list-group-item">Porta ac consectetur ac</button> 
         <button type="button" class="list-group-item">Vestibulum at eros</button> 
       </div> 
       <div class="m-t-1"><a href="#">View More</a></div> 
     </div> 

- our sidebar consists of three card blocks: (i) Recent Post; (ii) Archives; (iii) Categories

- div: we create a html tag <div> with a class of "list-group" inside our card.

- button: on this html tag, we insert a class "list-group-item" for every item of our list.

(3) Finally, go back to our file "index.ejs" and find the wrapper for the second column "<div class="col-md-4 hidden-md-down">. Remove its filler text and enter the following code:

     <%- partial("partial/_sidebar") %>

![][3]

[3]: images/extending-the-blogstrapi-theme/step-11--update-main-page.png

## Step 12: Update Blog Post Page

(1) In our root folder, open the file "blog-post.ejs", then head down to the page body section and find the wrapper "<div class="col-md-8">". Replace its filler text with the following code:

     <div class="card"> 
       <div class="card-block"> 
         <p><small>Posted by <a href="#">Admin</a> on January 1, 2016 in <a href="#">Category</a></small></p> 
         <p>Pellentesque habitant morbi tristique senectus et...</p> 
         <p><code>&lt;p&gt;this is what a code sample looks like&lt;/p&gt;</code></p> 
         <p>Pellentesque habitant morbi tristique senectus et netus...</p> 
         <!-- pre sample start //--> 
         <h4>pre sample code</h4> 
         <pre>This is what code will look like</pre> 
         <!-- pre sample end //--> 
         <!-- image //--> 
         <h4>responive image</h4> 
         <p><img src="img/image.jpg" class="img-fluid" alt="Responsive image"></p> 
         <!-- table //--> 
         <h4>table</h4> 
         <table class="table"> 
           <thead> 
             <tr> 
               <th>#</th> 
               <th>First Name</th> 
               <th>Last Name</th> 
               <th>Username</th> 
             </tr> 
           </thead> 
           <tbody> 
             <tr> 
               <th scope="row">1</th> 
               <td>john</td> 
               <td>smith</td> 
               <td>@jsmith</td> 
             </tr> 
             <tr> 
               <th scope="row">2</th> 
               <td>steve</td> 
               <td>stevens</td> 
               <td>@steve</td> 
             </tr> 
             <tr> 
               <th scope="row">3</th> 
               <td>mike</td> 
               <td>michaels</td> 
               <td>@mike</td> 
             </tr> 
           </tbody> 
         </table> 
       </div> 
     </div>

- No explanation is necessary here as this code contains all the previous concepts that were already explained.

(2) Find the wrapper for the second column "<div class="col-md-4 hidden-md-down">. Remove its filler text and enter the following code:

     <%- partial("partial/_sidebar") %>

(3) We will add a breadcrumb component to our page. After the container <div> at the top of the page, insert the following code:

     <div class="row m-t-1"> 
       <ol class="breadcrumb"> 
         <li><a href="#">Home</a></li> 
         <li><a href="#">Blog</a></li> 
         <li class="active">Post Title</li> 
       </ol> 
     </div> 

- ol: On the HTML ordered list tag, insert the class "breadcrumb".

- li: within the ordered list, we will simply create a list of links, and the last list item should have the class "active" on it.

(4) Finally, we will build a carousel in our blog post page. After the post title block of code, insert the following code:

     <div id="carousel-example-generic" class="carousel slide" data-ride="carousel"> 
       <ol class="carousel-indicators"> 
         <li data-target="#carousel-example-generic" data-slide-to="0" class="active"></li> 
         <li data-target="#carousel-example-generic" data-slide-to="1"></li> 
         <li data-target="#carousel-example-generic" data-slide-to="2"></li> 
       </ol> 
       <div class="carousel-inner" role="listbox"> 
         <div class="carousel-item active"> 
           <img src="..." alt="First slide"> 
         </div> 
         <div class="carousel-item"> 
           <img src="..." alt="Second slide"> 
         </div> 
         <div class="carousel-item"> 
           <img src="..." alt="Third slide"> 
         </div> 
       </div> 
       <a class="left carousel-control" href="#carousel-example-generic" role="button" data-slide="prev"> 
         <span class="icon-prev" aria-hidden="true"></span> 
         <span class="sr-only">Previous</span> 
       </a> 
       <a class="right carousel-control" href="#carousel-example-generic" role="button" data-slide="next"> 
         <span class="icon-next" aria-hidden="true"></span> 
         <span class="sr-only">Next</span> 
       </a> 
     </div> 

- div: The Carousel component starts with a HTML <div> tag and it needs a unique ID. Also, include the class  "slide". Finally, you need to add the attribute "data-ride" with a value of "carousel".

- ol: We use an ordered list for the navigation of carousel, by inserting the class "carousel-indicators".

- li: Each list require both the attributes "data-target", which needs to be the same unique ID as you gave your Carousel component, and "data-slide-to" that has the first value equal to 0, and increment by one for each list item after the first.

- div: The carousel content is wrapped with a <div> tag with a class of "carousel-inner" and attribute role with a value of "listbox".

- div: Within the outer <div>, each carousel item is a <div> tag that has a class of "carousel-item", and set the first item to "active".

- img: This is just a HTML tag for an image with no special classes.

- a: We have two anchor tags, that represent the left and right arrow, using the class "carousel-control" with classes "left" and "right" respectively. The attribute href needs to be the same unique ID as you gave your Carousel component, and set "role" attribute to "button". Finally, set the attribute "data-slide" to "prev" for your left arrow, and "next" for your right arrow, respectively.

- span: On this html tag, we insert a class of "icon-prev" for left arrow, and "icon-next" for right arrow respectively. This will render the arrow icons. Include the attribute "aria-hidden" and set it to true.

- span: Optionally, on this html tag, we insert a class "sr-only" for accessibility reasons. 