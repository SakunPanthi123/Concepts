## The powerful prototype object
prototype is an object that allows you to add methods and properties to all the instances of a particular contructor function like Arrays, functions, object. The prototype object is a fundamental part of JavaScript's inheritance model. It allows you to define properties and methods that can be shared across all instances of a particular constructor function. This is particularly useful for adding functionality to built-in objects like Array, Object, and Function.

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

If you look nicely, there is a callback function. When you use the .toBlob function in a canvas html element instance, you get to use the blob the below function returns as the callback function's parameter. This way you are able to do stuff withe the blob like uploading it to a server or saving it to the local file system. The .toBlob function is not a standard function in JavaScript, but it is a method of the HTMLCanvasElement interface that allows you to create a Blob object representing the image data contained in the canvas.

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
## The difference between UseForm type and UseFormReturn type

UseForm type is the props type of useForm hook that is used to manage form using react-hook-form. And UseFormReturn type is the return type of this hook. With UseForm type, you can pass in the props that you want to use in the useForm hook. The return values of useForm are setters and getters for values and errors as well as triggers for validation, handler for submission, register funciton for taking inputs and reset for resetting the form.

```typescript
export type UseFormProps<TFieldValues extends FieldValues = FieldValues, TContext = any> = Partial<{
    mode: Mode;
    disabled: boolean;
    reValidateMode: Exclude<Mode, 'onTouched' | 'all'>;
    defaultValues: DefaultValues<TFieldValues> | AsyncDefaultValues<TFieldValues>;
    values: TFieldValues;
    errors: FieldErrors<TFieldValues>;
    resetOptions: Parameters<UseFormReset<TFieldValues>>[1];
    resolver: Resolver<TFieldValues, TContext>;
    context: TContext;
    shouldFocusError: boolean;
    shouldUnregister: boolean;
    shouldUseNativeValidation: boolean;
    progressive: boolean;
    criteriaMode: CriteriaMode;
    delayError: number;
}>;
```
```typescript
export type UseFormReturn<TFieldValues extends FieldValues = FieldValues, TContext = any, TTransformedValues extends FieldValues | undefined = undefined> = {
    watch: UseFormWatch<TFieldValues>;
    getValues: UseFormGetValues<TFieldValues>;
    getFieldState: UseFormGetFieldState<TFieldValues>;
    setError: UseFormSetError<TFieldValues>;
    clearErrors: UseFormClearErrors<TFieldValues>;
    setValue: UseFormSetValue<TFieldValues>;
    trigger: UseFormTrigger<TFieldValues>;
    formState: FormState<TFieldValues>;
    resetField: UseFormResetField<TFieldValues>;
    reset: UseFormReset<TFieldValues>;
    handleSubmit: UseFormHandleSubmit<TFieldValues, TTransformedValues>;
    unregister: UseFormUnregister<TFieldValues>;
    control: Control<TFieldValues, TContext>;
    register: UseFormRegister<TFieldValues>;
    setFocus: UseFormSetFocus<TFieldValues>;
};
```

## The Context API of React

The createContext and useContext functions are part of the React Context API, which allows you to pass data through the component tree without having to pass props down manually at every level. The createContext function creates a new object. This object has two properties: Provider and Consumer. The Provider component has a property called value that can be used to pass data to any component that is a descendant of the Provider. The useContext function is a hook that allows you to access the value passed to the Provider from any component that is a descendant of the Provider.

```typescript
interface ProviderProps<T> {
        value: T;
        children?: ReactNode | undefined;
    }
```
```typescript

type Provider<T> = ProviderExoticComponent<ProviderProps<T>>;

 interface Context<T> {
        Provider: Provider<T>;
        Consumer: Consumer<T>;
        /**
         * Used in debugging messages. You might want to set it
         * explicitly if you want to display a different name for
         * debugging purposes.
         *
         * @see {@link https://legacy.reactjs.org/docs/react-component.html#displayname Legacy React Docs}
         */
        displayName?: string | undefined;
    }
```
```typescript
function createContext<T>(
        // If you thought this should be optional, see
        // https://github.com/DefinitelyTyped/DefinitelyTyped/pull/24509#issuecomment-382213106
        defaultValue: T,
    ): Context<T>;

    function useContext<T>(context: Context<T>): T;
```

## The clean up method of useEffect
The clean up method of useEffect is a function that is returned from the useEffect hook. This function is called when the component is unmounted or when the dependencies of the useEffect hook change. The clean up method is used to clean up any side effects that were created in the useEffect hook, such as removing event listeners or cancelling network requests.

```typescript
import { useState, useEffect } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log(`Effect runs for count: ${count}`);

    return () => {
      console.log(`Cleanup for count: ${count}`);
    };
  }, [count]);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increase</button>
    </div>
  );
}

```

## Dependency array of hooks
In react there is dependency of hooks like useEffect and useImperativeHandleRef that access current state and not stale closure of state values only when they are added in their dependency array.
```typescript
useImperativeHandle(ref, () => {
    return {
        async activateCamera() {
            const str = await navigator.mediaDevices.getUserMedia({
                video: { facingMode: "user" },
                audio: false
            }).catch(e=>{
                console.error(e)
            });
            if (str) {
                setStream(str)      
                setIsOpen(true)
                return true
            }
            return false
            

        },
        dismissCamera() {
            if (stream){
                stream.getTracks().forEach(track=>track.stop())
            }
            setIsOpen(false)
        },

    }
}, [stream]) // Add stream to the dependency array
```
Adding `stream` to the dependency array ensures that the `dismissCamera` function always has access to the current stream value, rather than a stale closure from the initial render.
