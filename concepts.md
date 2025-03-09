## The powerful prototype object
prototype is an object that allows you to add methods and properties to all the instances of a particular contructor function like Arrays, functions, object.
```typescript
Array.prototype.myFilter = function(callback, thisArg) {
    const result = []; // Array to store filtered elements

    for (let i = 0; i < this.length; i++) {
        if (i in this) { // Ensures we don't process empty slots in sparse arrays
            const value = this[i];
            if (callback.call(thisArg, value, i, this)) { // Call the callback with (value, index, array)
                result.push(value); // Add to result if callback returns true
            }
        }
    }

    return result; // Return the new filtered array
};

```
## How the .toBlob function could have been implemented
```typescript
HTMLCanvasElement.prototype.toBlob = function(callback, mimeType = "image/png", quality) {
    // Convert canvas data to base64 encoded string
    const dataURL = this.toDataURL(mimeType, quality);

    // Convert base64 to raw binary data
    const byteString = atob(dataURL.split(',')[1]);

    // Create a Uint8Array to store binary data
    const arrayBuffer = new Uint8Array(byteString.length);
    for (let i = 0; i < byteString.length; i++) {
        arrayBuffer[i] = byteString.charCodeAt(i);
    }

    // Create a Blob from the binary data
    const blob = new Blob([arrayBuffer], { type: mimeType });

    // Call the callback function with the created Blob
    callback(blob);
};
```

