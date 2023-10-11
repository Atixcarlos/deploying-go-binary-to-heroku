# Steps to deploy a GO binary to heroku

## steps:
### 1. Preparing files
**1.1. Modify your main file to allow Heroku to decide the port when hosted**
The modification is to handle the port for Heroku deployment.
```
package main
// ...
func main() {
	
    //...
	// Get port from environment variable.(Heroku)
	port := os.Getenv("PORT")
	if port == "" {
		port = "8081"
	}

	// Run HTTPS Server.
	err = http.ListenAndServe( ":" + port, nil)

    //...
}

```

**1.2. Compile your Go project**
```
GOOS=linux go build -o homeoner-api
```

**1.3. Copy your Go binary to a new folder where you'll also create the following files:**

The folder structure will look like this:
```
my-folder/
├── homeowner-api   # Go binary
├── package.json    # JSON configuration for the app
└── Procfile        # Heroku process type declaration
```
**1.4. Inside the [package.json] file, add the following:**

This file is a configuration for the Node.js application, which is needed by Heroku even for Go applications.

The trick is to make the installer think that it is going to use a certain Buildpack, but actually in your Procfile you override the control and execute a native binary directly. [https://medium.com/ki-labs-engineering/deploying-a-native-go-binary-on-heroku-6d4c955819d8]
```
{ "name": "appname", "version": "0.0.1", "dependencies": {} }
``` 

**1.5. Inside the [Procfile] file, add the following:**
This file tells Heroku how to run the application.
```
web: ./homeowner-api
```

### 2. Deployment
**2.1. Install the Heroku CLI (ubuntu)**
  Download and install the Heroku CLI.
  ```
  curl https://cli-assets.heroku.com/install-ubuntu.sh | sh
  ```

**2.2. log in to your Heroku account**
  ```
  $ heroku login
  ```

**2.3. For existing repositories, simply add the heroku remote**
  ```
  $ heroku git:remote -a homeowner-api
  ```

**2.4. commit to repository**
  ```
$ git add .
$ git commit -am "deploying our binary"
$ git push heroku master
  ```