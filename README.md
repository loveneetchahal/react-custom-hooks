## When to Use Custom Hooks in React? 
Custom hooks are a great fit whenever you need to reuse stateful logic across components, especially for complex tasks like data fetching, form handling, and authentication. They simplify your component code, enhance readability, and make testing and maintenance easier.

Another prime use case for custom hooks is when you find yourself writing the same code in multiple places. Instead of copying and pasting the same logic, you can extract it into a custom hook and reuse it wherever needed. This promotes the DRY (Don’t Repeat Yourself) principle, making your codebase more efficient and less error-prone.

Best Practices for Writing Custom Hooks
When creating custom hooks, there are several best practices to keep in mind to ensure they are effective and maintainable. Here are some key guidelines:

>  1. Start with use
Always start the name of your custom hook with use. This is a convention that helps other React developers identify these functions as hooks, ensuring the rules of hooks are applied correctly.

> 2. Keep Hooks Pure
Ensure that your hooks are pure functions. Avoid side effects directly inside your hooks; instead, manage them using useEffect or similar hooks.

Side effects are any operations or behaviors that occur in a component after rendering, and that don’t directly impact the current component render cycle.

> 3. Avoid Unnecessary Renders
Use memoization techniques like useMemo or useCallback to prevent your hooks from causing unnecessary re-renders, especially when dealing with expensive calculations or complex state updates.

Leverage built-in hooks such as useState, useEffect, useReducer, and useCallback to manage state and side effects within your custom hooks.

> 4. Return Consistent Value Types
Return an array or object with consistent value types from your hooks containing the states, functions, or any value you want to expose. This makes it clear what the hook provides and how to use it.

> 5. Write Tests and Document your Custom Hooks
Ensure your custom hooks are well-tested. Use tools like React Testing Library and Jest to write tests that validate the behavior of your hooks.

Provide clear documentation for your custom hooks. Explain what they do, their parameters, and their return values to make them easier for others (and yourself) to use and maintain.

> 6. Keep It Simple
Avoid making your hooks too complex. If a hook starts becoming too complicated, consider breaking it down into smaller, more focused hooks.

Make sure your hooks only have single responsibility.

> 7. Handle Errors Gracefully
Handle errors gracefully within your hooks. This ensures that your components using these hooks can handle unexpected scenarios without breaking.

*** Example Use Cases of Custom Hooks *** 
Here are some common use cases for custom hooks that you might encounter in your React projects:

### 1. Data Fetching
A custom hook for fetching data from an API endpoint can be reused across different components that need to fetch and display data.
```
import { useState, useEffect } from 'react';
 
function useFetch(url) {
	const [data, setData] = useState(null);
	const [loading, setLoading] = useState(true);
	const [error, setError] = useState(null);
 
	useEffect(() => {
		async function fetchData() {
			try {
				const response = await fetch(url);
				if (!response.ok) {
					throw new Error('Network response was not ok');
				}
				const result = await response.json();
				setData(result);
			} catch (err) {
				setError(err);
			} finally {
				setLoading(false);
			}
		}
 
		fetchData();
	}, [url]);
 
	return { data, loading, error };
}
```
> To use in component as per below:-
```
const Component = () => {
	const { data, loading, error } = useFetch('https://example.com/api/path');
 
	if (loading) return <div>Loading...</div>;
	if (error) return <div>Error: {error.message}</div>;
 
	return (
		<div>
			<h1>Data:</h1>
			<pre>{JSON.stringify(data)}</pre>
		</div>
	);
};
```
### 2. Form Handling
Custom hooks can manage form state, handle validation, and provide submit handlers, making form management a breeze.
```
import { useState } from 'react';
 
function useForm(initialValues) {
	const [values, setValues] = useState(initialValues);
	const [errors, setErrors] = useState({});
 
	const handleChange = (event) => {
		const { name, value } = event.target;
		setValues({
			...values,
			[name]: value
		});
	};
 
	const validate = (name, value) => {
		if (value.trim() === '') {
			setErrors((prevErrors) => ({ ...prevErrors, [name]: 'This field is required' }));
		} else {
			setErrors((prevErrors) => ({ ...prevErrors, [name]: '' }));
		}
	};
 
	const handleSubmit = (event, callback) => {
		event.preventDefault();
		if (Object.values(errors).every((err) => err === '')) {
			callback(values);
		}
	};
 
	return { values, handleChange, handleSubmit, validate, errors };
}
```
> To use in component as per below:-
```
const Component = () => {
	const { values, errors, handleChange, handleSubmit } = useForm({ username: '', password: '' }, validate);
 
	const submit = () => {
		alert('Form submitted successfully');
	};
 
	return (
		<form onSubmit={(e) => handleSubmit(e, submit)}>
			<div>
				<label>Username</label>
				<input type='text' name='username' value={values.username} onChange={handleChange} />
				{errors.username && <p>{errors.username}</p>}
			</div>
			<button type='submit'>Submit</button>
		</form>
	);
};
```
### 3. Authentication
Managing user authentication state, including login, logout, and checking if a user is authenticated.
```
import { useState, useEffect } from 'react';
 
function useAuth() {
	const [user, setUser] = useState(null);
 
	useEffect(() => {
		const loggedUser = localStorage.getItem('user');
		if (loggedUser) {
			setUser(JSON.parse(loggedUser));
		}
	}, []);
 
	const login = (userData) => {
		setUser(userData);
		// call login api here
		localStorage.setItem('user', JSON.stringify(userData));
	};
 
	const logout = () => {
		setUser(null);
		localStorage.removeItem('user');
	};
 
	return { user, login, logout };
}
```
### 4. Window Size
A custom hook for tracking window size changes, which can be useful for responsive design.
```
import { useState, useEffect } from 'react';
 
function useWindowSize() {
	const [size, setSize] = useState({
		width: window.innerWidth,
		height: window.innerHeight
	});
 
	useEffect(() => {
		const handleResize = () => {
			setSize({
				width: window.innerWidth,
				height: window.innerHeight
			});
		};
 
		window.addEventListener('resize', handleResize);
		return () => window.removeEventListener('resize', handleResize);
	}, []);
 
	return size;
}
```
### 5. Debouncing Input
A custom hook for debouncing input changes, which is useful for search inputs or other scenarios where you want to delay a function call.
```
import { useState, useEffect } from 'react';
 
function useDebounce(value, delay) {
	const [debouncedValue, setDebouncedValue] = useState(value);
 
	useEffect(() => {
		const handler = setTimeout(() => {
			setDebouncedValue(value);
		}, delay);
 
		return () => {
			clearTimeout(handler);
		};
	}, [value, delay]);
 
	return debouncedValue;
}
```
