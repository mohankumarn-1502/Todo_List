How to Deploying Django on Vercel
Deploying a Django application on Vercel from a GitHub repository is a streamlined process that leverages Vercel's integration with GitHub. This guide will walk you through the steps to redeploy your Django application on Vercel using your GitHub repository.

Prerequisites
Before you start, make sure you have the following:

A Django project ready for deployment.
A Vercel account.
Your Django project hosted on a GitHub repository.
Vercel CLI installed on your local machine (optional).
Step-by-Step Guide
1. Prepare Your Django Project
Ensure your Django project is ready for deployment. This includes having a requirements.txt file with all your dependencies listed.

2. Create a vercel.json Configuration File
Create a vercel.json file in the root directory of your Django project with the following content:

{
  "builds": [
    {
      "src": "projectname/wsgi.py",
      "use": "@vercel/python",
      "config": { "maxLambdaSize": "15mb", "runtime": "python3.12" }
    },
    {
      "src": "build_files.sh",
      "use": "@vercel/static-build",
      "config": { "distDir": ".vercel/output" }
    }
  ],
  "routes": [
    {
      "src": "/static/(.)",
      "dest": "/staticfiles/$1"
    },
    {
      "src": "/(.)",
      "dest": "projectname/wsgi.py"
    }
  ]
}
This configuration tells Vercel how to build and route your Django application.

3. Create a build_files.sh Script
Create a build_files.sh script in the root directory of your Django project with the following content:

#!/bin/bash

# Install dependencies
python3 -m pip install -r requirements.txt

# Collect static files
python3 manage.py collectstatic --noinput

# Create Vercel-compatible output vercel directory
mkdir -p .vercel/output/static
cp -r staticfiles/ .vercel/output/static/

python3 manage.py makemigrations
python3 manage.py migrate
Make sure to give execute permissions to the script:

chmod +x build_files.sh
4. Update Your WSGI Configuration
Ensure your wsgi.py file is correctly configured. It should look something like this:

"""
WSGI config for projectname project.

It exposes the WSGI callable as a module-level variable named ``application``.

For more information on this file, see
https://docs.djangoproject.com/en/5.0/howto/deployment/wsgi/
"""

import os

from django.core.wsgi import get_wsgi_application

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'projectname.settings')

application = get_wsgi_application()
app = application
5. Push Changes to GitHub
Commit and push your changes to your GitHub repository:

git add .
git commit -m "Prepare for Vercel deployment"
git push origin main
6. Deploy from GitHub
Login to Vercel: Go to Vercel and log in to your account.

Import GitHub Repository:

Click on the "New Project" button.
Select "Import Git Repository".
Choose your GitHub repository from the list.
Configure Project:

Vercel will automatically detect the vercel.json file and use it to configure the build and deployment process.
Click on the "Deploy" button to start the deployment process.
7. Configure Environment Variables
If your Django project relies on environment variables (e.g., for database configuration), you can set these in the Vercel dashboard under the "Settings" tab of your project.

8. Redeploy on Changes
Vercel will automatically redeploy your application whenever you push changes to the connected GitHub repository. This ensures that your application is always up-to-date with the latest code.

Conclusion
Redeploying a Django application on Vercel from a GitHub repository is a straightforward process that involves preparing your project, creating the necessary configuration files, and pushing your changes to GitHub. By following these steps, you can ensure that your Django application is always up-to-date and easily redeployable on Vercel.

Happy deploying!
