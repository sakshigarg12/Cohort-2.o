Middlewares -> middleware is simply a function that sits between the request and response 

app.use([path], middlewareFunction)
path (optional) → The route where this middleware should run. If not given, it applies to all routes.

How it Works
Every incoming request goes through a "pipeline" of middleware functions before getting a response.
app.use helps us put functions in that pipeline.

Example 1: Apply Middleware to All Routes
app.use((req, res, next) => {
  console.log('A request came:', req.method, req.url);
  next(); // move to the next middleware/route handler
});
✅ This will log every request no matter what the route is.

Example 2: Apply Middleware to a Specific Path
app.use('/api', (req, res, next) => {
  console.log('Request for /api route');
  next();
});
✅ This middleware runs only for routes starting with /api (like /api/users, /api/products).

Example 3: Using app.use to Serve Static Files
app.use(express.static('public'));
✅ This serves all files inside the public folder (like images, CSS, JS).
For example, public/logo.png becomes accessible at http://localhost:3000/logo.png.

Key Points
If you don’t call next(), the request will be stuck and never reach the next middleware or route handler.
Middleware runs in the order you define them.
app.use is often used for:
Logging
Authentication checks
Serving static files
Error handling

Middleware can be:

Check → Is the user logged in? Is the request valid?
Modify → Add new fields, parse body, attach user data
Log → Save request details, performance tracking
Handle → Serve static files, block requests, return error/response

Example : 
function authMiddleware(req, res, next) {
  if (req.query.token === "12345") {
    next(); // ✅ pass forward
  } else {
    res.status(401).send("Unauthorized"); // ❌ stop here
  }
}

app.use('/secure', authMiddleware);

app.get('/secure/data', (req, res) => {
  res.send("Sensitive data");
});

Step by Step

Request comes in →
Suppose the client calls:
GET /secure/data?token=12345
Express checks middlewares and routes in the order you defined them.
First it sees:
app.use('/secure', authMiddleware)
Since the path starts with /secure, this middleware runs.
Inside authMiddleware:
If token is valid → it calls next().
next() means → “go to the next thing in line that matches.”
If token is invalid → it sends the response immediately (and never calls next()).
If next() was called → Express continues scanning…
Next match is:
app.get('/secure/data', ...)
which is your route handler.
That sends "Sensitive data" as the final response.

📌 So the order is:

Request hits → /secure/data
Matches app.use('/secure', authMiddleware) → runs middleware
If next() → continue
If not → stop
Matches app.get('/secure/data', ...) → runs the route handler

Case 1: With a Path

app.use('/secure', authMiddleware);
Middleware runs only for requests starting with /secure.
Example: /secure/data, /secure/settings → middleware runs.
Example: /public → middleware does not run.

Case 2: Without a Path

app.use(authMiddleware);
Now the middleware runs for every single request in the app.

No matter what the path is → /, /login, /about, /secure/data → this middleware will always be executed first (in order).
app.use(path, middleware) → middleware applies only to routes starting with path.
app.use(middleware) → middleware applies globally to all requests.

