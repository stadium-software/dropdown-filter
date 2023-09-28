# DropDown Filter

When dropdowns contain many items, finding an item can be cumbersome and frustrating. Here is a simple example of how to add a filter to a dropdown. 

https://github.com/stadium-software/dropdown-filter/assets/2085324/0118ecde-1d76-471a-8308-127d3f31fd0a

## Application Setup
1. Check the *Enable Style Sheet* checkbox in the application properties

## Script Setup
1. Create a Global Script called "FilterDropdown"
2. Add two Input parameters to the Global Script
   1. CaseSensitive (a boolean)
   2. DropDownClassName (a string)
3. Drag a JavaScript action into the script
4. Add the Javascript below into the JavaScript code property (ignore the validation error message "Invalid script was detected")
```
const className = ~.Parameters.Input.DropDownClassName;
const caseSensitive = ~.Parameters.Input.CaseSensitive;
const ddContainer = document.querySelector("." + className);
ddContainer.classList.add("stadium-dropdown-filter");
const dd = ddContainer.querySelector("select");
const parent = dd.parentElement;

let container = document.createElement("div");
container.classList.add("dropdown-filter-container");
parent.appendChild(container);

const ev = new Event("change");
dd.addEventListener("change", () => { }, false);

let input = document.createElement("input");
input.classList.add("form-control");
input.setAttribute("placeholder", "Filter");
container.appendChild(input);

function populateFilterOptions() {
    let optionsDiv = ddContainer.querySelector(".dropdown-filter-options-container");
    if (optionsDiv) {
        optionsDiv.remove();
    }

    let optionsContainer = document.createElement("div");
    optionsContainer.classList.add("dropdown-filter-options-container");

    let options = dd.options;
    for (let i = 0; i < options.length; i++) {
        if (!options[i].classList.contains("option-hint")) {
            let option = document.createElement("div");
            option.classList.add("dropdown-filter-option");
            option.setAttribute("value", options[i].value);
            option.textContent = options[i].text;
            option.addEventListener("click", function (e) {
                dd.value = e.target.getAttribute("value");
                let hintOption = dd.querySelector(".option-hint");
                if (hintOption) dd.querySelector(".option-hint").classList.add("hide");
                dd.classList.remove("select-option-hint");
                container.classList.remove("show");
                resetDropDown();
                dd.dispatchEvent(ev);
            });
            optionsContainer.appendChild(option);
        }
    }
    container.appendChild(optionsContainer);
}
function resetDropDown() {
    let filterOptions = ddContainer.querySelectorAll(".dropdown-filter-option");
    for (let i = 0; i < filterOptions.length; i++) {
        filterOptions[i].classList.remove("hide");
    }
    input.value = "";
}

populateFilterOptions();
input.addEventListener("input", function () {
    let filterOptions = ddContainer.querySelectorAll(".dropdown-filter-option");
    for (let i = 0; i < filterOptions.length; i++) {
        let needle = input.value;
        let heystack = filterOptions[i].innerHTML;
        if (!caseSensitive) { 
            needle = needle.toLowerCase();
            heystack = heystack.toLowerCase();
        }
        if (heystack.indexOf(needle) == -1) {
            filterOptions[i].classList.add("hide");
        } else {
            filterOptions[i].classList.remove("hide");
        }
    }
});
input.addEventListener("blur", function () {
    if (input.value == "") {
        resetDropDown();
    }
});
dd.addEventListener("mousedown", function (e) {
    e.preventDefault();
    container.classList.toggle("show");
    input.focus();
});
document.body.addEventListener("click", function (e) {
    if (!e.target.closest("." + className)) {
        container.classList.remove("show");
        resetDropDown();
    }
});
let options = {
    characterData: true,
    attributes: false,
    childList: true,
    subtree: true,
    characterDataOldValue: true,
},
observer = new MutationObserver(populateFilterOptions);
observer.observe(dd, options);
```

## DropDown Setup
1. Drag a DropDown Control to the page
2. Add a unique classname of your choise to the DropDown classes property (e.g. my-dropdown-filter)
3. Populate the DropDown with data (static, in the Page.Load event or in any other script)

## Page.Load Event Setup
1. Drag the "FilterDropdown" script into the Page.Load event
2. Enter the classname of the FilterDropdown into the *DropDownClassName* input parameter (e.g. my-dropdown-filter)
3. If you want the filter to be case insensitive, add *false* into the *CaseSensitive* Input Parameter (it's true by default)

## Customising CSS
The *filter-dropdown-variables.css* file included in this repo contains a set of variables that can be changed to customise the dropdown filter. Follow the steps below to customise your implementation
1. Open the CSS file called [*filter-dropdown-variables.css*](filter-dropdown-variables.css) from this repo in an editor of your choice (I recommend [VS Code](https://code.visualstudio.com/))
2. Adjust the variables in the *:root* element as you see fit

## Applying the CSS
How to apply the CSS to your application
1. Create a folder called *CSS* inside of your Embedded Files in your application
2. Drag the two CSS files from this repo [*filter-dropdown-variables.css*](filter-dropdown-variables.css) and [*filter-dropdown.css*](filter-dropdown.css) into that folder

#### Stadium 6 (versions 6.6 and above)
1. Paste the link tags below into the *head* property of your application
```
<link rel="stylesheet" href="{EmbeddedFiles}/CSS/filter-dropdown.css">
<link rel="stylesheet" href="{EmbeddedFiles}/CSS/filter-dropdown-variables.css">
``` 

## CSS Upgrading
To upgrade thje CSS in this module, follow the [steps outlined in this repo](https://github.com/stadium-software/samples-upgrading)
