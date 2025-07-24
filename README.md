# TeamSync Architecture Documentation

This repository contains the network architecture diagrams for the TeamSync backend system, viewable as a GitHub Pages site.

## 🚀 Quick Setup

### 1. Initialize Git and commit files
```bash
git init
git add .
git commit -m "feat: initial commit with architecture diagrams"
```

### 2. Create GitHub repository
- Go to [GitHub](https://github.com/new)
- Create a new repository named `TeamSync` (or your preferred name)
- Don't initialize with README, .gitignore, or license

### 3. Push to GitHub
```bash
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/TeamSync.git
git push -u origin main
```

### 4. Enable GitHub Pages
1. Go to your repository on GitHub
2. Click on **Settings** tab
3. Scroll down to **Pages** section (in the left sidebar)
4. Under **Source**, select **Deploy from a branch**
5. Under **Branch**, select `main` and `/root`
6. Click **Save**

### 5. Access your site
Your site will be available at: `https://YOUR_USERNAME.github.io/TeamSync/`

It may take a few minutes for the site to become available.

## 📁 Repository Structure

- `index.html` - Main page that renders the Mermaid diagrams
- `diagram1.mmd` - Network architecture diagram
- `diagram2.mmd` - Video conference architecture diagram
- `logo.jpg` - Company logo (required)
- `_config.yml` - Jekyll configuration for GitHub Pages
- `README.md` - This file

## 🎨 Features

- Beautiful GitHub-styled markdown rendering
- Clean Mermaid diagrams with black text
- Responsive design for mobile and desktop
- Simple, static diagram display

## 🛠️ Local Development

To preview the site locally:

1. Install Python (if not already installed)
2. Run a simple HTTP server:
   ```bash
   python -m http.server 8000
   ```
3. Open `http://localhost:8000` in your browser

## 📝 Updating Diagrams

To update the diagrams:
1. Edit the `diagram.md` file
2. Commit and push changes:
   ```bash
   git add diagram.md
   git commit -m "doc: update architecture diagrams"
   git push
   ```
3. GitHub Pages will automatically update within a few minutes

## 🔧 Troubleshooting

If diagrams don't render:
- Check browser console for errors
- Ensure Mermaid syntax is correct
- Clear browser cache and refresh

## 📄 License

This project documentation is proprietary to TeamSync. 