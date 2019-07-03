Before you can scan images with MicroScanner you'll need to sign up for a token - this is used to rate-limit the number of images each user can scan. 

### Step 1 - Sign up for a new token

If you have previously signed up and you already have a MicroScanner token, you can skip this step and go straight to step 2.

`read -p "Enter your email address: " email`{{execute}}

`docker run --rm -it aquasec/microscanner --register $email`{{execute}}

You'll receive your token by email.

### Step 2 - Enter your token

The next command will prompt you for the token so that it can be stored in an environment variable:

`read -p "Enter your MicroScanner token: " token`{{execute}}

## Next step

Now that you have a token, let's move on to writing a Dockerfile that will run MicroScanner on your image. 
