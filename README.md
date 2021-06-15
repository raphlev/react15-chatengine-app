# Chat engine react app

- using Firebase Auth + Firebase Firestore + Forebase Storage + ChatEngine.io + other react UI components
- we don't use Firebase Hosting, instead we deploy this app on vercel
- Serverless functions are built using these steps:
  create a function (which can access secrets and 3rd party APIs)
  deploy it to a platform
  call it as an API from JavaScript (just using something as simple as fetch)
  Looking into several services (hardcore AWS, Cloudflare Workers, Serverless, and more) but using the Vercel platform turned out to be the simplest solution.
  https://madewithlove.com/blog/software-engineering/serverless-functions-with-vercel/
  https://blog.logrocket.com/serverless-deployments-vercel-node-js/

# Credidts

## source of this project

- https://github.com/portexe/react-chatengine-demo/tree/master
- https://www.youtube.com/watch?v=ODoxVFQKmBc

# Implementation

## To debug code

From within jsx component, best practice is to add console.log inside a useEffect of a component

- useEffect(() => { console.log('My Object: ', myObject); }, [myObject]);

## During Testing in Chrome

- on localhost:3000 > Dev Tools > Application > Storage > Clear Site Data OR Local Storage > select localhost:3000 > Clear

## Firebase account

console.firebase

- create app
- activate user Auth: auth > Sign-in method > Email/Password: Enable
- create test user (necessary during development phase to debug, not necessary if deployment for production): auth > Add user with existing email and set any password here > copy userUID for Storage - this user will be also used to test and debug the Login.jsx form
- Cloud Firestore > create database > test mode
- cloud firestore create dummy database (necessary during development phase to debug, not necessary if deployment for production): start collection > collection = chatUsers > Document ID = paste the userUID > FIELDS: avatar = string; userName = string > Save
  ->Firebase Storage to upload and store user avatar images
  it is used to store new image uploaded to change the avatar
  ->Firebase Firestore to store the URL to each user's avatar as a DB collection entry
  purpose of creating a firestore DB is for the 2 functionnalities provided to a authenticated user to configure its personal data: 1°) change/import its avatar image and store it in DB 2°) Change the userName that is displayed in the chatengine (may be different from the firebase auth user)
- Firebase > project overview > settings > There is no web app > add firebase to web app > Do not set up Firebase Hosting since it will be deployed on Vercel > copy the firebaseConfig
  apiKey: "AIzaSyC0l17T8MBR3df_XLIoXPP4WJ51IXHgPBw",
  authDomain: "chatengine-94abf.firebaseapp.com",
  projectId: "chatengine-94abf",
  storageBucket: "chatengine-94abf.appspot.com",
  messagingSenderId: "1094542386816",
  appId: "1:1094542386816:web:d071b2818a1a0f3be0a865"

## chatengine.io

- create project >
  projectID=b3d213aa-c4d6-4a63-932f-49d451800fb3  --> to be used in ChatContext.jsk > projectID
  private key=839a2ed7-c732-46f0-a180-b5fb1afd67bf --> to be used by chat_engine_private_key variable
- create dummy user (necessary during development phase to debug, not necessary if deployment for production): New User > any user name, Secret = paste the userUID (common key and uniq) > create person, copy the username value
- create new Chat with user created as admin (for testing purpose during development phase, not needed for production deployment)

## console.firebase: link the username we jsut created

- Development Phase only - not needed for production
  auth > Cloud Firestore > create database > start colection > collection = chatUsers > Document ID = paste the userUID > FIELDS: avatar = string; userName value = paste the user name > Save

## jsconfig.json

- contains the baseUrl = ./src folder to specify that src is the base URL for the import
- with this file, we can later remove the relative path ../../XXX when importing modules in jsx components and directly import them as absolute
- also looking at app.jsx, it simplifies the import modules as it can now be de-structured from components folder since each folder componet contains some index.js which exports the relative module
- see https://code.visualstudio.com/docs/languages/jsconfig

## setup prettier with .prettierrc file

- settings > workspace > format > select esbn.prettier...
- settings > workspace > prettier > path > ./prettierrc

## .env : not needed

- can be used temporarely before using vercel
- we will remove .env file after since we will deploy the app into vercel and using the vercel cli to load env var
- prefix REACT_APP to all env variables, don't need to install dotenv !!!!
- then use process.env.REACT_APP_XXX in your app to use them: see firebase.js
- restart server (npm start) after updating anf adding process.env in the app

## Vercel used to stored servless functions, node is used as a platform as serveless functions

- create account to vercel
- install Vercel cli : npm i -g vercel
- back in vscode terminal, create a vercel project and link it to our app in the cloud:
  vercel -v
  vercel dev > y > link to existing preoject = no > proejct's name = vscode directory > in which directory is code located = ./ > override settings = No
  CHECK: set the API key in the Vercel cli !!!

## from now on, enter 'vercel dev' to serve our application and it will load our servless functions

## vercel serverless functions

- we write our servelss function as APIs and hosted in the vercel cloud, and to do this we need to create an 'api' folder at same level as 'src' folder this way vercel knows that we have serverless functions !!!
- It is a more secure way to use private key on the api serverless function since it is backend code and technically nobody will access to it since it will not be in some browser code!!
- on a backend server we don't have fetch api by default, that's why we use axios to write apis lib from backend
- api/createUser.js > we use axios > client browser will pass information to req - here it will be userId and userName > process.env.chat_engine_private_key SHOULD be created in vercel environment (not as REACT_APP env since it is backend code not in the browser !!)
- setup vercel env variables:
  1-Add 'REACT_APP_XX' envs: vercel env add > Plaintext > proceed on each of the variables REACT_APP_XX variables which corresponds to the firebase config keys:
  see the firebase.js which contains the process.env.REACT_APP_XXX
  2-Add chat_engine_private_key env: vercell env add > Plaintext > chat_engine_private_key env value is taken from chatengine account and copy the private key
  3-MAKE SURE TO DELETE ANY .env file otherwise it will conflicts !!

chat_engine_private_key = 839a2ed7-c732-46f0-a180-b5fb1afd67bf
REACT_APP_API_KEY = AIzaSyC0l17T8MBR3df_XLIoXPP4WJ51IXHgPBw
REACT_APP_AUTH_DOMAIN = chatengine-94abf.firebaseapp.com
REACT_APP_PROJECT_ID = chatengine-94abf
REACT_APP_STORAGE_BUCKET = chatengine-94abf.appspot.com
REACT_APP_MESSAGING_SENDER_ID = 1094542386816
REACT_APP_APP_ID = 1:1094542386816:web:d071b2818a1a0f3be0a865
- list vercel environment variables (server side)
vercel env ls
- Environment Variables:   https://vercel.com/docs/cli#commands/env
Environment Variables created for the Development Environment can be downloaded into a local development setup using the vercel env pull command provided by Vercel CLI:
vercel env pull
Running the command will create a .env file in the current directory, which can then be consumed by your framework's Development Command (like next dev).
If you're using vercel dev, there's no need to run vercel env pull, as vercel dev automatically downloads the Development Environment Variables into memory.
Basic usage
vercel env ls: Using the vercel env command to list all Environment Variables in a Project.
vercel env add: Using the vercel env command to add an Environment Variable to a Project.
vercel env rm: Using the vercel env command to remove an Environment Variable from a Project.
vercel env pull: Using the vercel env command to download Development Environment Variables from the cloud and write to a local .env file.

## Formik = easy react popular library for handling forms > integrated with Yup > easy to use with integrated api

- needs 4 props: see Signup.jsx
- we need yup for forms validation > yup describes a validation schema with shape pattern that is easy to understand, and it includes string message for each pattern if validation fails
- we pass in a function as child element of Formik since we need to get some variables from Formik ccomponent to process field validations and to process button click
  is Valid: automatically generated from validationSchema
  isSubmitting: boolean that detects if form is sublitted
  this function returns the HTML form with the definition of the input fields
- Formik onSubmit function will pass in 2 objects:
  . object with the input fields values that we can de-structured
  . setSubmitting : is one of the helper function that is provided by Formik component in order to set isSubmitting imperatively. see doc

## Auth with Firebase

- user signs up for access to the Chat application
- we call serverless function to create the user on the chatengine.io side using our ID from firebase auth
- we create a chatUser object abd add it to our Firebase Firestore database
- The user is then redirected to the main route so that it does not stay on the login/signUp component

## Create custom hooks useAuth.js to store the user autenticated, using useEffect that can returns a clean-up function

- useEffect clean-up function is executed when the component unmounts.
- However, effects run for every render (dependency array) and not just once.
- This is why useEffect clean-up function is also executed before running the effects next time to clean up effects from the previous render
- it is useful in case of async function called in useEffect and returns values after component did unmount so we don't want to update state which could cause to re-reender again if state is in the depend array
- user can be either undefined (default), firebase.user or null : null is considered as a value !!

## Create custom hooks useResolved.js that returns true if passed variable(s) are NOT-UNDEFINED and returns false in all other case (can be either set to value or set to null)

## SignUp.jsx: create auth flow by code (until now above we have created manually user in firebase and also in chatengine.io)

- call firebase createUser api
- send request with fetch (frontend) to the /api/createUser serverless function already serving by vercel and in turns the createUser.js api function send request with axios (backend) to chatengine.io to create user
- the firebase createUser api returned UserUID will be used for chatengine userId

## useChat.js is a custom hook that is created to get rid of useContext or useState from within all components

- returns all objects stored in the react context

## Chat.jsx

- using ChatEngine from chatengine.io, there is onConnect prop event that is called when successfull connection to that chatEngine

## Chat toolbar, create mmultiple user, search, etc..

- ChatToolbar.jsx
- SearchUsers.jsx
- debouncing while searching for users: do not send request to server on each key stroke to reduce server calls
- useDebounce.js hook

## Sending Message

- Chat.jsx: more events: we need to hook on chatengine.io onNewChat, onNewMessage, onDeleteChat to update the UI without having to refresh
- ChatInput.jsx
- MessageList.jsx
- GroupMessage.jsx: will group the message if same user in order to not display each time the avatar on consecutive meesage sent by same user
- useScrollToBottom.js custom hook: this hook is used to scroll automatically to bottom when new messages added from other users otherwise need to manually scroll the container.
  need to wait until all images loaded to the browser document DOM because images are loaded async to browser and text message will be loaded instantantly

## Sending Images

- in this version we focus only on images, but chatEngine could manage any type of files to be sent to other users !!
- ImageUpload.jsx > upload avatars
  it is using input html component that is set display to hidden (classname=file-input), and we replace OOTB input html component with react component ImageUpload in order to display an icon for file upload

## Uploading Avatars

- Firebase Storage to upload and store user avatar images
- Firebase Firestore to store the URL to each user's avatar as a DB collection entry
- adding ability to upload images to Firebase
- RailHeader.jsx
  it is using input html component that is set display to hidden (classname=file-input), and we replace OOTB input html component with react component ImageUpload to manage ui as we want
  see: uploading avatar should use REACT_APP_STORAGE_BUCKET config from Firebase which set a firebase

# Deployment

- upload to GITHUB > react15-chatengine-app > master branch
- vercel account > link github account > project - GIT > repository name = react15-chatengine-app > save..

# Issues

- add display none into Chat.jsx > ChatEngine component to fix the hideUI={true} that does not work
- TypeError: selectedChat.messages is not iterable: Chat.jsx
    52 | if (selectedChat && chatId === selectedChat.id) {
    53 |   setSelectedChat({
    54 |     ...selectedChat,
  > 55 |     messages: [...selectedChat.messages, message],
       | ^  56 |   });
- production env (vercel): I needed to update the firebase package to a more recent release otherwise build was failing, I took latest one 8.6.5. I properly setup the 7 env variables in the vercel CLI as done in the video, but I still have an issue at run-time with the env variables. auth/invalid-api-key: Your API key is invalid, please check you have copied it correctly.. some changes in firebase SDK ?! Maybe someone got same issue?
