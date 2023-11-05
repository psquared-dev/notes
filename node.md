we can also add route handlers using app.use using the following syntax:

app.use("/home", (req, res) => {
res.send("hello")
})

However this would always be run regardless
of the type of req method

Also, this middleware will only be run when the user visits the /home route only.

---

parse json and set the body attribute to req object
app.use(express.json());

this option is onyl effective when data is passed as JSON not form-url-encoded

to make the server aware that we're sending JSON, we set the header to
content-type: application/json

for form-url-encoded use

app.use(express.urlencoded({ extended: true }));

---

/products/:id <- id is the url parameter
to access url parameters use req.params

---

to access query params we use req.query

---

- res.send() is used to send the string
- res.json() is used to send the json
- res.xml() is used to send the xml
- res.status(201).send() can be used to set status and send reponse at the same time
  at time when you want to send empty response just with status code use res.sendStatus()
- to set the header we use res.set("content-type", "application/xml");
- to access the headers we use req.headers

---

- to set cookie we use res.cookie("cart", "cart");
- to access cookie from req object we have to install cookie-parser and add middleware
  app.use(cookieParser());
  all cookies can now be accessed via req.cookies

---

by convention to create and endpoint to dispalay list of entity we use plural. For e.g:

/users - for displaying list of users
/todos - for displaying list of todos

by convention for search endpoint we use plural. For e.g:

/users/:userId
/users/:userId/comments/:commentId

by convention for create endpoint we use plural. For e.g:

/users/

--

reloading node app automatically

install nodemon and the following to the scripts object

"start": "nodemon ./index.js"
