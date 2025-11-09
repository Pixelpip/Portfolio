---
title: "From Zero to Blog: My Hexo, Cactus & Vercel Setup"
date: 2025-08-15 18:48:14
tags:
  - Hexo
  - Vercel
  - Cactus
  - Tutorial
  - GitHub
  - Web Development
categories:
  - Tutorials
---

Ever wanted a blazing-fast, professional-looking blog that's completely free to host and fully under your control? In this post, I'm breaking down my entire setup. We'll go from an empty folder to a deployed blog using a powerful stack: the **Hexo** static site generator, the minimalist **Cactus** theme, and hosting on **Vercel**.

As a bonus, I'll show you how I got a custom domain for free using the **GitHub Student Developer Pack**. Let's get started! 

***

## The Stack: Why These Tools?

* **Hexo:** A fast and simple static site generator powered by Node.js. You write posts in Markdown, and Hexo compiles them into a complete website with plain HTML, CSS, and JavaScript. No databases, no slow server-side rendering.
* **Cactus Theme:** A clean, minimalist, and responsive theme for Hexo.
* **Vercel:** A cloud platform for static sites. Their free tier is incredibly generous, and deployment is as simple as a `git push`.
* **GitHub Student Developer Pack:** A bundle of free tools and services for students. It includes free domains from name.com , namcheap and few others, which is good enough for a personal protfolio.

***

## Prerequisites 

Before we dive in, make sure you have a few things ready:

* **Node.js and npm:** You can check if you have them by running `node -v` and `npm -v` in your terminal.
* **Git:** Essential for version control and deploying.
* **A GitHub Account:** This is where we'll store our code and how we'll connect to Vercel.
* **GitHub Student Developer Pack:** You'll need to be an eligible student to get the free domain.

***

## Step 1: Installing and Setting Up Hexo

First things first, let's get Hexo running locally. Open your terminal and run these commands.

```bash
# Install the Hexo CLI (Command Line Interface) globally
npm install -g hexo-cli

# Create a new blog directory and navigate into it
hexo init my-blog
cd my-blog

# Install the necessary dependencies
npm install

# Start the local server to see your blog in action!
hexo server 
```

Now, open your browser and go to http://localhost:4000. You should see the default Hexo blog. Awesome!

***

## Step 2: Applying the Cactus Theme 

The default theme is fine, but we want that clean Cactus look.

Clone the Cactus theme right into your themes folder:

```sh
    $ git clone https://github.com/probberechts/hexo-theme-cactus.git themes/cactus
```

Configure Hexo to use the theme. Open the main configuration file for your site, `_config.yml`, located in the root of your project. Find the theme setting and change it:

```yaml
# Find this line in _config.yml
theme: landscape

# And change it to this
theme: cactus
```

Restart the hexo server (use Ctrl+C in the terminal to stop it first), and refresh your browser. Your blog should now have the minimalist Cactus aesthetic. You can further customize the theme by editing its own configuration file at `themes/cactus/_config.yml`.

***

## Step 3: Deploying with Vercel 

1. Push your project to GitHub. Create a new, empty repository on GitHub and push your Hexo blog's code to it.
2. Make sure you're in your blog's directory:

```bash
git init
git add .
git commit -m "Initial commit with Hexo and Cactus"
git remote add origin YOUR_GITHUB_REPO_URL
git push -u origin main
```

3. Import your project into Vercel.
4. Sign up for a Vercel account using your GitHub profile.
5. On your dashboard, click "Add New... -> Project".
6. Select your blog's repository from the list.
7. Vercel will automatically detect that it's a Hexo project and configure the build settings for you. The defaults are perfect.
8. Click Deploy.

In a minute or two, your site will be live on a `.vercel.app` domain. Even better, every time you push a new commit to your main branch on GitHub, Vercel will automatically redeploy your site with the changes.

***

## Step 4: Adding a Free Custom Domain

A `.vercel.app` URL is cool, but a custom domain is the final professional touch.

- Get the Pack: If you're a student, head over to the GitHub Education page and apply. It's a quick process.
- Claim Your Domain: Once approved, browse the offers for a free domain name from partners like Namecheap or .TECH (I had bought a .app domain from name.com for the first time). Follow their instructions to register your domain for one year, completely free.

Connect the Domain to Vercel:

1. In your Vercel project dashboard, go to Settings -> Domains.
2. Enter your custom domain and click Add.
3. Vercel will provide you with the DNS records you need to add (usually an A record or a CNAME record).
4. Go to your domain registrar's (e.g., Namecheap's) DNS management panel and add the records exactly as Vercel instructed.

DNS changes can take a little while to propagate across the internet, but soon your blog will be accessible via your very own custom domain.

> And that's it! You now have a professional, fast, and free blog that you have complete ownership of. Happy writing! 
