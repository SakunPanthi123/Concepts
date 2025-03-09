# The powerful prototype object
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
