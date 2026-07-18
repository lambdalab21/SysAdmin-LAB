#deployment 

## Recommended local structure

On his development machine:
```
site1/
  src/
    index.html
    style.css
    about.html
  assets/
    logo.png
  dist/
  build.sh
  deploy.sh
  README.md
  .gitignore
```


For a simple static site, `src/` and `dist/` may look almost the same at first. That is okay. The point is the habit:

- `src/` = where he edits
- `dist/` = what is safe to deploy
- `/var/www/site1/public/` = what Nginx serves

## Git

```bash
cd site1
git init
git add .
git commit -m "initial commit"
git git remote add origin https://github.com
git push -u origin main
```

## Deployment

Then deploy:

```bash
cp ./src/* ./dist/public/
scp -r ./dest/ deploy@app01:/var/www/site1/
```


``` bash
cp ./src/. ./dist/public/
scp -r ./dist/. deploy@app01:/var/www/site1/public/
```

Purpose: understand remote copy.

