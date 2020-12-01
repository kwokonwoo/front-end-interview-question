```js
loadScript() {
  return new Promise((resolve, reject) => {
    const script = document.createElement('script');
    script.type = 'text/javascript';
    script.async = true;
    script.src = `${this.protocol}//${this.src}`
    
    const el = document.getElementsByTagName('script')[0];
    el.parentNode.insertBefore(script, el);
    
    script.addEventListener('load', () => {
      this.isLoaded = true;
      resolve(script)
    })
    
    script.addEventListener('error', () => {
      reject(new Error(`${this.src} failed to load.`))
    })
  })
}
```
