# Guide: React CRUD practice

## Backend setup

- In your terminal, navigate to your Rails backend app and run your server: `rails server`
- Make sure each RESTful route (index, create, show, update, destroy) is working by manually testing each request.
- Make sure to configure the [rack-cors](https://github.com/cyu/rack-cors) gem to allow web requests from `localhost:5173` (instructions [here](https://gist.github.com/peterxjang/77d6243cf85103b027a56b401b62b289)).

## Frontend setup

- In a new terminal window, navigate to your Actualize directory and create a new project:
  ```
  npm create vite@latest
  ```

  _When prompted, enter a project name, select the React framework, select the JavaScript variant_

- In the terminal, go to your new directory, install the dependencies, and start the dev server:
  ```
  cd <name-of-your-app>
  npm install
  npm run dev
  ```

- In a new terminal tab, install the axios library:
  ```
  npm install axios --save
  ```

- In the terminal, open your text editor for the frontend app:
  ```
  code .
  ```

- In your text editor, add the following line to <strong>.eslint.config.js</strong>
  
  ```diff
    import js from '@eslint/js'
    import globals from 'globals'
    import react from 'eslint-plugin-react'
    import reactHooks from 'eslint-plugin-react-hooks'
    import reactRefresh from 'eslint-plugin-react-refresh'
    
    export default [
      {
        files: ['**/*.{js,jsx}'],
        ignores: ['dist'],
        languageOptions: {
          ecmaVersion: 2020,
          globals: globals.browser,
          parserOptions: {
            ecmaVersion: 'latest',
            ecmaFeatures: { jsx: true },
            sourceType: 'module',
          },
        },
        settings: { react: { version: '18.3' } },
        plugins: {
          react,
          'react-hooks': reactHooks,
          'react-refresh': reactRefresh,
        },
        rules: {
          ...js.configs.recommended.rules,
          ...react.configs.recommended.rules,
          ...react.configs['jsx-runtime'].rules,
          ...reactHooks.configs.recommended.rules,
          'react/jsx-no-target-blank': 'off',
  +       'react/prop-types': 'off',
          'react-refresh/only-export-components': [
            'warn',
            { allowConstantExport: true },
          ],
        },
      },
    ]
  ```
- In your text editor, make a new file <strong>src/Header.jsx</strong>:

  ```jsx
  export function Header() {
    return (
      <header>
        <nav>
          <a href="#">Home</a> | <a href="#">Link</a>
        </nav>
      </header>
    )
  }
  ```

- In your text editor, make a new file <strong>src/<ins>Photos</ins>Page.jsx</strong>:

  ```jsx
  export function PhotosPage() {
    return (
      <main>
        <h1>Welcome to React!</h1>
      </main>
    )
  }
  ```

- In your text editor, make a new file <strong>src/Footer.jsx</strong>:

  ```jsx
  export function Footer() {
    return (
      <footer>
        <p>Copyright 2022</p>
      </footer>
    )
  }
  ```

- In your text editor, open the file src/App.jsx and replace the code with the following:

  ```jsx
  import { Header } from "./Header";
  import { PhotosPage } from "./PhotosPage";
  import { Footer } from "./Footer";

  function App() {
    return (
      <div>
        <Header />
        <PhotosPage />
        <Footer />
      </div>
    )
  }

  export default App;
  ```

- In your text editor, open the file src/index.css and replace all the CSS code with the following:

  ```css
  html {
    scroll-behavior: smooth;
  }
  ```
  
- In your browser, visit http://localhost:5173 to make sure your app is working!

  > In your terminal, use git to save a version of your code.
  > ```bash
  > git init
  > git add --all
  > git commit -m "Initial commit"
  > ```

## CRUD Single Page Application

- Index action

  - <details><summary>Make a new <strong>src/<ins>Photos</ins>Index.jsx</strong> file that returns placeholder HTML</strong></summary>
  
      ```jsx
      export function PhotosIndex() {
        return (
          <div>
            <h1>All photos</h1>
          </div>
        );
      }
      ```
      </details>

  - <details><summary>In <strong>src/<ins>Photos</ins>Page.jsx</strong>, import the <strong>src/<ins>Photos</ins>Index.jsx</strong> component</summary>
      
      ```diff
      + import { PhotosIndex } from "./PhotosIndex";
  
        export function PhotosPage() {
          return (
            <main>
      -       <h1>Welcome to React!</h1>
      +       <PhotosIndex />
            </main>
          );
        }
      ```
      </details>
      
    > Check the browser to see the placeholder HTML from the index component.
    
  - <details><summary>In <strong>src/<ins>Photos</ins>Page.jsx</strong>, pass placeholder data to the index component as props</summary>
      
      ```diff
        import { PhotosIndex } from "./PhotosIndex";
  
        export function PhotosPage() {
      +   const photos = [
      +     {id: 1, name: "First", url: "https://via.placeholder.com/150", width: 150, height: 150},
      +     {id: 2, name: "Second", url: "https://via.placeholder.com/300", width: 300, height: 300},
      +   ];
  
          return (
            <main>
      -       <PhotosIndex />
      +       <PhotosIndex photos={photos} />
            </main>
          );
        }
      ```
      </details>
      
  - <details><summary>In <strong>src/<ins>Photos</ins>Index.jsx</strong>, map through the array given in props</summary>
      
      ```diff
      - export function PhotosIndex() {
      + export function PhotosIndex({ photos }) {
          return (
            <div>
              <h1>All photos</h1>
      +       {photos.map((photo) => (
      +         <div key={photo.id}>
      +           <h2>{photo.name}</h2>
      +           <img src={photo.url} />
      +           <p>Width: {photo.width}</p>
      +           <p>Height: {photo.height}</p>
      +         </div>
      +       ))}
            </div>
          );
        }
      ```
      </details>
      
    > Check the browser to see the placeholder data in the index component.
    
  - <details><summary>In <strong>src/<ins>Photos</ins>Page.jsx</strong>, use the useState and useEffect hooks to define the data via a backend web request</summary>
      
      ```diff
      + import axios from "axios";
      + import { useState, useEffect } from "react";
        import { PhotosIndex } from "./PhotosIndex";
  
        export function PhotosPage() {
      -   const photos = [
      -     {id: 1, name: "First", url: "https://via.placeholder.com/150", width: 150, height: 150},
      -     {id: 2, name: "Second", url: "https://via.placeholder.com/300", width: 300, height: 300},
      -   ];
  
      +   const [photos, setPhotos] = useState([]);

      +   const handleIndex = () => {
      +     console.log("handleIndex");
      +     axios.get("http://localhost:3000/photos.json").then((response) => {
      +       console.log(response.data);
      +       setPhotos(response.data);
      +     });
      +   };

      +   useEffect(handleIndex, []);
  
          return (
            <main>
              <PhotosIndex photos={photos} />
            </main>
          );
        }
      ```
      </details>
      
    > Check the browser to see the real data from the backend in the index component.
    > 
    > In your terminal, use git to save a version of your code.
    > ```bash
    > git add --all
    > git commit -m "Add index action"
    > ```
- New/Create actions

  - <details><summary>Make a new <strong>src/<ins>Photos</ins>New.jsx</strong> file that returns an HTML form</strong></summary>
  
      ```jsx
      export function PhotosNew() {
        return (
          <div>
            <h1>New Photo</h1>
            <form>
              <div>
                Name: <input name="name" type="text" />
              </div>
              <div>
                Url: <input name="url" type="text" />
              </div>
              <div>
                Width: <input name="width" type="text" />
              </div>
              <div>
                Height: <input name="height" type="text" />
              </div>
              <button type="submit">Create</button>
            </form>
          </div>
        );
      }
      ```
      </details>

  - <details><summary>In <strong>src/<ins>Photos</ins>Page.jsx</strong>, import the <strong>src/<ins>Photos</ins>New.jsx</strong> component</summary>
      
      ```diff
        import axios from "axios";
        import { useState, useEffect } from "react";
        import { PhotosIndex } from "./PhotosIndex";
      + import { PhotosNew } from "./PhotosNew";

        export function PhotosPage() {
          const [photos, setPhotos] = useState([]);

          const handleIndex = () => {
            console.log("handleIndex");
            axios.get("http://localhost:3000/photos.json").then((response) => {
              console.log(response.data);
              setPhotos(response.data);
            });
          };

          useEffect(handleIndex, []);

          return (
            <main>
      +       <PhotosNew />
              <PhotosIndex photos={photos} />
            </main>
          );
        }
      ```
      </details>
      
    > Check the browser to see the HTML form in the new component.

  - <details><summary>In <strong>src/<ins>Photos</ins>Page.jsx</strong>, make a create function and pass it to the new component</summary>
      
      ```diff
  
        import axios from "axios";
        import { useState, useEffect } from "react";
        import { PhotosIndex } from "./PhotosIndex";
        import { PhotosNew } from "./PhotosNew";

        export function PhotosPage() {
          const [photos, setPhotos] = useState([]);

          const handleIndex = () => {
            console.log("handleIndex");
            axios.get("http://localhost:3000/photos.json").then((response) => {
              console.log(response.data);
              setPhotos(response.data);
            });
          };

      +   const handleCreate = (params, successCallback) => {
      +     console.log("handleCreate", params);
      +     axios.post("http://localhost:3000/photos.json", params).then((response) => {
      +       setPhotos([...photos, response.data]);
      +       successCallback();
      +     });
      +   };
  
          useEffect(handleIndex, []);

          return (
            <main>
      -       <PhotosNew />
      +       <PhotosNew onCreate={handleCreate} />
              <PhotosIndex photos={photos} />
            </main>
          );
        }
      ```
      </details>

  - <details><summary>In <strong>src/<ins>Photos</ins>New.jsx</strong>, handle the submit event to run the given props create function</strong></summary>
  
      ```diff
      - export function PhotosNew() {
      + export function PhotosNew({ onCreate }) {

      +   const handleSubmit = (event) => {
      +     event.preventDefault();
      +     const params = new FormData(event.target);
      +     onCreate(params, () => event.target.reset());
      +   };

          return (
            <div>
              <h1>New Photo</h1>
      -       <form>
      +       <form onSubmit={handleSubmit}>
                <div>
                  Name: <input name="name" type="text" />
                </div>
                <div>
                  Url: <input name="url" type="text" />
                </div>
                <div>
                  Width: <input name="width" type="text" />
                </div>
                <div>
                  Height: <input name="height" type="text" />
                </div>
                <button type="submit">Create</button>
              </form>
            </div>
          );
        }
      ```
      </details>
      
    > Check the browser to fill in the HTML form and create a new item.
    > 
    > In your terminal, use git to save a version of your code.
    > ```bash
    > git add --all
    > git commit -m "Add new/create actions"
    > ```
    
- Show action

  - <details><summary>Make a new <strong>src/Modal.css</strong> file with the following content:</summary>
  
    ```css
    .modal-background {
      display: block;
      position: fixed;
      top: 0;
      left: 0;
      width:100%;
      height: 100%;
      background: rgba(0, 0, 0, 0.6);
      z-index: 1000;
    }

    .modal-main {
      position: fixed;
      background: white;
      width: 80%;
      height: auto;
      top: 50%;
      left: 50%;
      transform: translate(-50%,-50%);
      padding: 1em;
    }

    .modal-main button.close {
      font-size: 2em;
      background: none;
      border: none;
      position: absolute;
      top: 0em;
      right: 0.2em;
    }
    ```
    </details>

  - <details><summary>Make a new <strong>src/Modal.jsx</strong> file with the following content:</summary>
  
    ```jsx
    import "./Modal.css";

    export function Modal({ children, show, onClose }) {
      if (show) {
        return (
          <div className="modal-background">
            <section className="modal-main">
              {children}
              <button className="close" type="button" onClick={onClose}>
                &#x2715;
              </button>
            </section>
          </div>
        );
      }
    }
    ```
    </details>

  - <details><summary>In <strong>src/<ins>Photos</ins>Page.jsx</strong>, import the modal component</summary>
  
    ```diff
      import axios from "axios";
      import { useState, useEffect } from "react";
      import { PhotosIndex } from "./PhotosIndex";
      import { PhotosNew } from "./PhotosNew";
    + import { Modal } from "./Modal";

      export function PhotosPage() {
        const [photos, setPhotos] = useState([]);

        const handleIndex = () => {
          console.log("handleIndex");
          axios.get("http://localhost:3000/photos.json").then((response) => {
            console.log(response.data);
            setPhotos(response.data);
          });
        };

        const handleCreate = (params, successCallback) => {
          console.log("handleCreate", params);
          axios.post("http://localhost:3000/photos.json", params).then((response) => {
            setPhotos([...photos, response.data]);
            successCallback();
          });
        };
  
        useEffect(handleIndex, []);

        return (
          <main>
            <PhotosNew onCreate={handleCreate} />
            <PhotosIndex photos={photos} />
    +       <Modal show={true}>
    +         <h1>Test</h1>
    +       </Modal>
          </main>
        );
      }
    ```
    </details>
    
    > Check the browser to see the modal with placeholder HTML

  - <details><summary>In <strong>src/<ins>Photos</ins>Page.jsx</strong>, make state and functions to show and hide the modal</summary>
  
    ```diff
      import axios from "axios";
      import { useState, useEffect } from "react";
      import { PhotosIndex } from "./PhotosIndex";
      import { PhotosNew } from "./PhotosNew";
      import { Modal } from "./Modal";

      export function PhotosPage() {
        const [photos, setPhotos] = useState([]);
    +   const [isPhotosShowVisible, setIsPhotosShowVisible] = useState(false);
    +   const [currentPhoto, setCurrentPhoto] = useState({});
  
        const handleIndex = () => {
          console.log("handleIndex");
          axios.get("http://localhost:3000/photos.json").then((response) => {
            console.log(response.data);
            setPhotos(response.data);
          });
        };

        const handleCreate = (params, successCallback) => {
          console.log("handleCreate", params);
          axios.post("http://localhost:3000/photos.json", params).then((response) => {
            setPhotos([...photos, response.data]);
            successCallback();
          });
        };
  
    +   const handleShow = (photo) => {
    +     console.log("handleShow", photo);
    +     setIsPhotosShowVisible(true);
    +     setCurrentPhoto(photo);
    +   };

    +   const handleClose = () => {
    +     console.log("handleClose");
    +     setIsPhotosShowVisible(false);
    +   };
  
        useEffect(handleIndex, []);

        return (
          <main>
            <PhotosNew onCreate={handleCreate} />
    -       <PhotosIndex photos={photos} />
    +       <PhotosIndex photos={photos} onShow={handleShow} />
    -       <Modal show={true}>
    +       <Modal show={isPhotosShowVisible} onClose={handleClose}>
              <h1>Test</h1>
            </Modal>
          </main>
        );
      }
    ```
    </details>
  
  - <details><summary>In <strong>src/<ins>Photos</ins>Index.jsx</strong>, make a button to show the modal</summary>
  
    ```diff
    - export function PhotosIndex({ photos }) {
    + export function PhotosIndex({ photos, onShow }) {
        return (
          <div>
            <h1>All photos</h1>
            {photos.map((photo) => (
              <div key={photo.id}>
                <h2>{photo.name}</h2>
                <img src={photo.url} />
                <p>Width: {photo.width}</p>
                <p>Height: {photo.height}</p>
    +           <button onClick={() => onShow(photo)}>More info</button>
              </div>
            ))}
          </div>
        );
      }
    ```
    </details>
            
    > Check the browser to test showing and closing the modal with the appropriate buttons
            
  - <details><summary>Make a new <strong>src/<ins>Photos</ins>Show.jsx</strong> file that renders attributes from props</summary>
  
    ```jsx
    export function PhotosShow({ photo }) {
      return (
        <div>
          <h1>Photo information</h1>
          <p>Name: {photo.name}</p>
          <p>Url: {photo.url}</p>
          <p>Width: {photo.width}</p>
          <p>Height: {photo.height}</p>
        </div>
      );
    }
    ```
    </details>

  - <details><summary>In <strong>src/<ins>Photos</ins>Page.jsx</strong>, import the show component and use it in the modal</summary>
  
    ```diff
      import axios from "axios";
      import { useState, useEffect } from "react";
      import { PhotosIndex } from "./PhotosIndex";
      import { PhotosNew } from "./PhotosNew";
    + import { PhotosShow } from "./PhotosShow";
      import { Modal } from "./Modal";

      export function PhotosPage() {
        const [photos, setPhotos] = useState([]);
        const [isPhotosShowVisible, setIsPhotosShowVisible] = useState(false);
        const [currentPhoto, setCurrentPhoto] = useState({});
  
        const handleIndex = () => {
          console.log("handleIndex");
          axios.get("http://localhost:3000/photos.json").then((response) => {
            console.log(response.data);
            setPhotos(response.data);
          });
        };

        const handleCreate = (params, successCallback) => {
          console.log("handleCreate", params);
          axios.post("http://localhost:3000/photos.json", params).then((response) => {
            setPhotos([...photos, response.data]);
            successCallback();
          });
        };
  
        const handleShow = (photo) => {
          console.log("handleShow", photo);
          setIsPhotosShowVisible(true);
          setCurrentPhoto(photo);
        };

        const handleClose = () => {
          console.log("handleClose");
          setIsPhotosShowVisible(false);
        };
  
        useEffect(handleIndex, []);

        return (
          <main>
            <PhotosNew onCreate={handleCreate} />
            <PhotosIndex photos={photos} onShow={handleShow} />
            <Modal show={isPhotosShowVisible} onClose={handleClose}>             
    -         <h1>Test</h1>
    +         <PhotosShow photo={currentPhoto} />
            </Modal>
          </main>
        );
      }
    ```
    </details>
    
    > Check the browser to see the selected item attributes within the modal
    > 
    > In your terminal, use git to save a version of your code.
    > ```bash
    > git add --all
    > git commit -m "Add show action"
    > ```

- Edit/Update actions

  - <details><summary>Modify <strong>src/<ins>Photos</ins>Show.jsx</strong> to include an edit form with default values</summary>
  
    ```diff
      export function PhotosShow({ photo }) {
        return (
          <div>
            <h1>Photo information</h1>
            <p>Name: {photo.name}</p>
            <p>Url: {photo.url}</p>
            <p>Width: {photo.width}</p>
            <p>Height: {photo.height}</p>
    +       <form>
    +         <div>
    +           Name: <input defaultValue={photo.name} name="name" type="text" />
    +         </div>
    +         <div>
    +           Url: <input defaultValue={photo.url} name="url" type="text" />
    +         </div>
    +         <div>
    +           Width: <input defaultValue={photo.width} name="width" type="text" />
    +         </div>
    +         <div>
    +           Height: <input defaultValue={photo.height} name="height" type="text" />
    +         </div>
    +         <button type="submit">Update</button>
    +       </form>
          </div>
        );
      }
    ```
    </details>

    > Check the browser to see the edit HTML form within the modal

  - <details><summary>In <strong>src/<ins>Photos</ins>Page.jsx</strong>, make an update function and pass it into the show component</summary>
  
    ```diff
      import axios from "axios";
      import { useState, useEffect } from "react";
      import { PhotosIndex } from "./PhotosIndex";
      import { PhotosNew } from "./PhotosNew";
      import { PhotosShow } from "./PhotosShow";
      import { Modal } from "./Modal";

      export function PhotosPage() {
        const [photos, setPhotos] = useState([]);
        const [isPhotosShowVisible, setIsPhotosShowVisible] = useState(false);
        const [currentPhoto, setCurrentPhoto] = useState({});
  
        const handleIndex = () => {
          console.log("handleIndex");
          axios.get("http://localhost:3000/photos.json").then((response) => {
            console.log(response.data);
            setPhotos(response.data);
          });
        };

        const handleCreate = (params, successCallback) => {
          console.log("handleCreate", params);
          axios.post("http://localhost:3000/photos.json", params).then((response) => {
            setPhotos([...photos, response.data]);
            successCallback();
          });
        };
  
        const handleShow = (photo) => {
          console.log("handleShow", photo);
          setIsPhotosShowVisible(true);
          setCurrentPhoto(photo);
        };
            
    +   const handleUpdate = (id, params, successCallback) => {
    +     console.log("handleUpdate", params);
    +     axios.patch(`http://localhost:3000/photos/${id}.json`, params).then((response) => {
    +       setPhotos(
    +         photos.map((photo) => {
    +           if (photo.id === response.data.id) {
    +             return response.data;
    +           } else {
    +             return photo;
    +           }
    +         })
    +       );
    +       successCallback();
    +       handleClose();
    +     });
    +   };

        const handleClose = () => {
          console.log("handleClose");
          setIsPhotosShowVisible(false);
        };
  
        useEffect(handleIndex, []);

        return (
          <main>
            <PhotosNew onCreate={handleCreate} />
            <PhotosIndex photos={photos} onShow={handleShow} />
            <Modal show={isPhotosShowVisible} onClose={handleClose}>
    -         <PhotosShow photo={currentPhoto} />
    +         <PhotosShow photo={currentPhoto} onUpdate={handleUpdate} />
            </Modal>
          </main>
        );
      }
    ```
    </details>

  - <details><summary>In <strong>src/<ins>Photos</ins>Show.jsx</strong>, handle the form submit to run the props update function</summary>
  
    ```diff
    - export function PhotosShow({ photo }) {
    + export function PhotosShow({ photo, onUpdate }) {
            
    +   const handleSubmit = (event) => {
    +     event.preventDefault();
    +     const params = new FormData(event.target);
    +     onUpdate(photo.id, params, () => event.target.reset());
    +   };
            
        return (
          <div>
            <h1>Photo information</h1>
            <p>Name: {photo.name}</p>
            <p>Url: {photo.url}</p>
            <p>Width: {photo.width}</p>
            <p>Height: {photo.height}</p>
    -       <form>
    +       <form onSubmit={handleSubmit}>
              <div>
                Name: <input defaultValue={photo.name} name="name" type="text" />
              </div>
              <div>
                Url: <input defaultValue={photo.url} name="url" type="text" />
              </div>
              <div>
                Width: <input defaultValue={photo.width} name="width" type="text" />
              </div>
              <div>
                Height: <input defaultValue={photo.height} name="height" type="text" />
              </div>
              <button type="submit">Update</button>
            </form>
          </div>
        );
      }
    ```
    </details>

    > Check the browser to edit an attribute using the HTML form and submit the form
    > 
    > In your terminal, use git to save a version of your code.
    > ```bash
    > git add --all
    > git commit -m "Add edit/update actions"
    > ```
  
- Destroy action

  - <details><summary>In <strong>src/<ins>Photos</ins>Page.jsx</strong>, make a destroy function and pass it into the show component</summary>
  
    ```diff
      import axios from "axios";
      import { useState, useEffect } from "react";
      import { PhotosIndex } from "./PhotosIndex";
      import { PhotosNew } from "./PhotosNew";
      import { PhotosShow } from "./PhotosShow";
      import { Modal } from "./Modal";

      export function PhotosPage() {
        const [photos, setPhotos] = useState([]);
        const [isPhotosShowVisible, setIsPhotosShowVisible] = useState(false);
        const [currentPhoto, setCurrentPhoto] = useState({});

        const handleIndex = () => {
          console.log("handleIndex");
          axios.get("http://localhost:3000/photos.json").then((response) => {
            console.log(response.data);
            setPhotos(response.data);
          });
        };

        const handleCreate = (params, successCallback) => {
          console.log("handleCreate", params);
          axios.post("http://localhost:3000/photos.json", params).then((response) => {
            setPhotos([...photos, response.data]);
            successCallback();
          });
        };

        const handleShow = (photo) => {
          console.log("handleShow", photo);
          setIsPhotosShowVisible(true);
          setCurrentPhoto(photo);
        };

        const handleUpdate = (id, params, successCallback) => {
          console.log("handleUpdate", params);
          axios.patch(`http://localhost:3000/photos/${id}.json`, params).then((response) => {
            setPhotos(
              photos.map((photo) => {
                if (photo.id === response.data.id) {
                  return response.data;
                } else {
                  return photo;
                }
              })
            );
            successCallback();
            handleClose();
          });
        };

    +   const handleDestroy = (id) => {
    +     console.log("handleDestroy", id);
    +     axios.delete(`http://localhost:3000/photos/${id}.json`).then(() => {
    +       setPhotos(photos.filter((photo) => photo.id !== id));
    +       handleClose();
    +     });
    +   };

        const handleClose = () => {
          console.log("handleClose");
          setIsPhotosShowVisible(false);
        };

        useEffect(handleIndex, []);

        return (
          <main>
            <PhotosNew onCreate={handleCreate} />
            <PhotosIndex photos={photos} onShow={handleShow} />
            <Modal show={isPhotosShowVisible} onClose={handleClose}>
    -         <PhotosShow photo={currentPhoto} onUpdate={handleUpdate} />
    +         <PhotosShow photo={currentPhoto} onUpdate={handleUpdate} onDestroy={handleDestroy} />
            </Modal>
          </main>
        );
      }
    ```
    </details>

  - <details><summary>In <strong>src/<ins>Photos</ins>Show.jsx</strong>, make a button to run the props destroy function on click</summary>
  
    ```diff
    - export function PhotosShow({ photo, onUpdate }) {
    + export function PhotosShow({ photo, onUpdate, onDestroy }) {
        const handleSubmit = (event) => {
          event.preventDefault();
          const params = new FormData(event.target);
          onUpdate(photo.id, params, () => event.target.reset());
        };

        return (
          <div>
            <h1>Photo information</h1>
            <form onSubmit={handleSubmit}>
              <div>
                Name: <input defaultValue={photo.name} name="name" type="text" />
              </div>
              <div>
                Url: <input defaultValue={photo.url} name="url" type="text" />
              </div>
              <div>
                Width: <input defaultValue={photo.width} name="width" type="text" />
              </div>
              <div>
                Height: <input defaultValue={photo.height} name="height" type="text" />
              </div>
              <button type="submit">Update</button>
            </form>
    +       <button onClick={() => onDestroy(photo.id)}>Destroy</button>
          </div>
        );
      }
    ```
    </details>

    > Check the browser to destroy a photo by clicking the destroy button
    > 
    > In your terminal, use git to save a version of your code.
    > ```bash
    > git add --all
    > git commit -m "Add destroy action"
    > ```

## Routes

- In your terminal, install react router
  ```bash
  npm install react-router-dom
  ```
  
- Modify `src/App.jsx` to use React Router
  ```jsx
  import { createBrowserRouter, RouterProvider, Outlet } from "react-router-dom";

  import { Header } from "./Header";
  import { SignupPage } from "./SignupPage";
  import { LoginPage } from "./LoginPage";
  import { PhotosPage } from "./PhotosPage";
  import { Footer } from "./Footer";
  
  const router = createBrowserRouter([
    {
      element: (
        <div>
          <Header />
          <Outlet />
          <Footer />
        </div>
      ),
      children: [
        {
          path: "/",
          element: <PhotosPage />,
        },
        {
          path: "/signup",
          element: <SignupPage />,
        },
        {
          path: "/login",
          element: <LoginPage />,
        },
      ],
    },
  ]);

  function App() {
    return <RouterProvider router={router} />;
  }
  
  export default App;
  ```

- In `src/Header.jsx`, import the Link component and replace <a> elements with <Link> elements (example below):
  ```jsx
  import { Link } from "react-router-dom";

  // ...

    <Link to="/signup">Signup</Link>
  ```

- Example standalone index page

  - <details><summary>Make a new <strong>src/<ins>Photos</ins>IndexPage.jsx</strong> file</summary>
  
      ```jsx
      import { useLoaderData, useNavigate } from "react-router-dom";

      import { PhotosIndex } from "./PhotosIndex";

      export function PhotosIndexPage() {
        const photos = useLoaderData();
        const navigate = useNavigate();

        const handleShow = (photo) => {
          console.log("handleShow", photo);
          navigate(`/photos/${photo.id}`);
        };

        return (
          <div>
            <PhotosIndex photos={photos} onShow={handleShow} />
          </div>
        );
      }
      ```
      </details>

  - <details><summary>Modify <strong>src/App.jsx</strong> to add the page to the routes</summary>
  
      ```diff
      + import axios from "axios";
        import { createBrowserRouter, RouterProvider, Outlet } from "react-router-dom";

        import { Header } from "./Header";
        import { SignupPage } from "./SignupPage";
        import { LoginPage } from "./LoginPage";
        import { PhotosPage } from "./PhotosPage";
      + import { PhotosIndexPage } from "./PhotosIndexPage";
        import { Footer } from "./Footer";
        
        const router = createBrowserRouter([
          {
            element: (
              <div>
                <Header />
                <Outlet />
                <Footer />
              </div>
            ),
            children: [
              {
                path: "/",
                element: <PhotosPage />,
              },
              {
                path: "/signup",
                element: <SignupPage />,
              },
              {
                path: "/login",
                element: <LoginPage />,
              },
      +       {
      +         path: "/photos",
      +         element: <PhotosIndexPage />,
      +         loader: () => axios.get("http://localhost:3000/photos.json").then((response) => response.data),
      +       },
            ],
          },
        ]);

        function App() {
          return <RouterProvider router={router} />;
        }
        
        export default App;
      ```
      </details>

- Example standalone new page

  - <details><summary>Make a new <strong>src/<ins>Photos</ins>NewPage.jsx</strong> file</summary>
  
      ```jsx
      import axios from "axios";
      import { useNavigate } from "react-router-dom";

      import { PhotosNew } from "./PhotosNew";

      export function PhotosNewPage() {
        const navigate = useNavigate();

        const handleCreate = (params) => {
          console.log("handleCreate", params);
          axios.post("http://localhost:3000/photos.json", params).then((response) => {
            console.log(response);
            navigate("/photos");
          });
        };

        return (
          <div>
            <PhotosNew onCreate={handleCreate} />
          </div>
        );
      }
      ```
      </details>

  - <details><summary>Modify <strong>src/App.jsx</strong> to add the page to the routes</summary>
  
      ```diff
        import axios from "axios";
        import { createBrowserRouter, RouterProvider, Outlet } from "react-router-dom";

        import { Header } from "./Header";
        import { SignupPage } from "./SignupPage";
        import { LoginPage } from "./LoginPage";
        import { PhotosPage } from "./PhotosPage";
        import { PhotosIndexPage } from "./PhotosIndexPage";
      + import { PhotosNewPage } from "./PhotosNewPage";
        import { Footer } from "./Footer";
        
        const router = createBrowserRouter([
          {
            element: (
              <div>
                <Header />
                <Outlet />
                <Footer />
              </div>
            ),
            children: [
              {
                path: "/",
                element: <PhotosPage />,
              },
              {
                path: "/signup",
                element: <SignupPage />,
              },
              {
                path: "/login",
                element: <LoginPage />,
              },
              {
                path: "/photos",
                element: <PhotosIndexPage />,
                loader: () => axios.get("http://localhost:3000/photos.json").then((response) => response.data),
              },
      +       {
      +         path: "/photos/new",
      +         element: <PhotosNewPage />,
      +       },
            ],
          },
        ]);

        function App() {
          return <RouterProvider router={router} />;
        }
        
        export default App;
      ```
      </details>

- Example standalone show page

  - <details><summary>Make a new <strong>src/<ins>Photos</ins>ShowPage.jsx</strong> file</summary>
  
      ```jsx
      import axios from "axios";
      import { useLoaderData, useNavigate } from "react-router-dom";

      import { PhotosShow } from "./PhotosShow";

      export function PhotosShowPage() {
        const photo = useLoaderData();
        const navigate = useNavigate();

        const handleUpdate = (id, params) => {
          console.log("handleUpdate", params);
          axios.patch(`http://localhost:3000/photos/${id}.json`, params).then(() => {
            navigate("/photos");
          });
        };

        const handleDestroy = (id) => {
          console.log("handleDestroy", id);
          axios.delete(`http://localhost:3000/photos/${id}.json`).then(() => {
            navigate("/photos");
          });
        };

        return (
          <div>
            <PhotosShow photo={photo} onUpdate={handleUpdate} onDestroy={handleDestroy} />
          </div>
        );
      }
      ```
      </details>

  - <details><summary>Modify <strong>src/App.jsx</strong> to add the page to the routes</summary>
  
      ```diff
        import axios from "axios";
        import { createBrowserRouter, RouterProvider, Outlet } from "react-router-dom";

        import { Header } from "./Header";
        import { SignupPage } from "./SignupPage";
        import { LoginPage } from "./LoginPage";
        import { PhotosPage } from "./PhotosPage";
        import { PhotosIndexPage } from "./PhotosIndexPage";
        import { PhotosNewPage } from "./PhotosNewPage";
      + import { PhotosShowPage } from "./PhotosShowPage";
        import { Footer } from "./Footer";
        
        const router = createBrowserRouter([
          {
            element: (
              <div>
                <Header />
                <Outlet />
                <Footer />
              </div>
            ),
            children: [
              {
                path: "/",
                element: <PhotosPage />,
              },
              {
                path: "/signup",
                element: <SignupPage />,
              },
              {
                path: "/login",
                element: <LoginPage />,
              },
              {
                path: "/photos",
                element: <PhotosIndexPage />,
                loader: () => axios.get("http://localhost:3000/photos.json").then((response) => response.data),
              },
              {
                path: "/photos/new",
                element: <PhotosNewPage />,
              },
      +       {
      +         path: "/photos/:id",
      +         element: <PhotosShowPage />,
      +         loader: ({ params }) => axios.get(`http://localhost:3000/photos/${params.id}.json`).then((response) => response.data),
      +       },
            ],
          },
        ]);

        function App() {
          return <RouterProvider router={router} />;
        }
        
        export default App;
      ```
      </details>

- Read more about React router options:
  https://reactrouter.com/en/main/start/tutorial


## Styling

### Option 1: Vanilla CSS
- Add CSS to the `src/index.css` file
- Example: https://gist.github.com/peterxjang/c07e2df3e619ab08c8f661d187e7a728 

### Option 2: Bootstrap CSS
- In your terminal, install Bootstrap using npm:
  ```bash
  npm install bootstrap --save
  ```
- In your text editor `src/main.jsx`, import bootstrap
  ```javascript
  import "bootstrap";
  import "bootstrap/dist/css/bootstrap.min.css";
  ```
- In your text editor `src/App.jsx`, add a "container" class for margin/padding
  ```jsx
  <div className="container">
  ```
- Use examples from https://getbootstrap.com/ to style your app!

### Option 3: Tailwind CSS
- Install tailwind (start at Step 2 Install Tailwind CSS): https://tailwindcss.com/docs/guides/vite
- Use examples from https://tailwindcss.com/ to style your app!
  - Example h1
    ```jsx
    <h1 className="text-3xl font-bold underline">
    ```
  - Example button
    ```jsx
    <button className="rounded border border-gray-300 p-2 hover:bg-gray-100">
    ```
  - Example app layout
    ```jsx
    <div className="flex min-h-screen flex-col">
      <Header />
      <div className="container mx-auto flex-auto p-4">
        <PhotosPage />
      </div>
      <Footer />
    </div>
    ```
  - Example cards in a grid
    ```jsx
    <div className="grid grid-cols-3 gap-4">
      {recipes.map((recipe) => (
        <div key={recipe.id} className="rounded shadow-lg mb-4">
          <h2>{recipe.title}</h2>
          <img src={recipe.image_url} />
          <p>Chef: {recipe.chef}</p>
          <p>Ingredients: {recipe.ingredients}</p>
          <button onClick={() => onSelectRecipe(recipe)}>
            More info
          </button>
        </div>
      ))}
    </div>
    ```
- Install tailwind forms plugin: https://github.com/tailwindlabs/tailwindcss-forms
  - Example input
    ```jsx
    <input type="text" className="mt-1 block w-full rounded-md border-gray-300" />
    ```
