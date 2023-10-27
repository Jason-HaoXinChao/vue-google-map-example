
## Vue Google Map
By: Hao Xin Chao

This project is a basic application with the following functionalities:
* A search box with autocomplete functionality for the user to input an address.
* A search button for the search box, the user can also search by pressing enter while focused on the search box.
* A button that prompts the user for permission to acquire their current location.
* A map with a marker labelling each of the obtained locations.
* The timezone name and local time of the last searched location is displayed.

The locations obtained from search box and the user's browser will be stored in a table. The table has the following features:
* Pagination, displaying maximum of 10 records per page.
* A checkbox at the beginning of each row to let the user select multiple records at the same time.
* A delete button on the top to remove all selected records as well as their markers on the map. The button is only enabled if at least one record is selected.


### Built With

Below are languages/frameworks/libraries used to build this project. I tried to keep it as minimal as possible.

* Vue3
* Vuetify (for UI)
* JavaScript/HTML/CSS
* [js-api-loader](https://www.npmjs.com/package/@googlemaps/js-api-loader "js-api-loader npm page") (for loading google maps API)
* [Axios](https://www.npmjs.com/package/axios "axios npm page") (for making api request to google's timezone API)



<!-- GETTING STARTED -->
## Instructions

Before building the project. please make sure you have the latest npm and node.js build installed.

Below are instructions on how to run the application. 

#### Install Dependencies

  ```sh
  npm install
  ```
#### Run build
  ```sh
  npm run dev
  ```
Now the application should be assessble  from `http://localhost:5173/`. Please feel free to contact me if there are any questions or concerns.

