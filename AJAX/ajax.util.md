# AJAX Utility

## Query

```js
const Query = (url, type, data = null) => {
  return new Promise((resolve, reject) => {
    let ajaxOptions = {
      url: url,
      method: type,
      dataType: "json",
      success: function (result) {
        resolve(result);
      },
      error: function (err) {
        reject(err);
      },
    };

    if (["PUT", "POST", "PATCH", "DELETE"].includes(type) && data) {
      ajaxOptions.data = JSON.stringify(data);
      ajaxOptions.contentType = "application/json";
    }

    $.ajax(ajaxOptions);
  });
};
```

## Usage

```js
Query("/api", "GET")
  .then((response) => {
    //Do something with the response like loadTable() or populateTable()
  })
  .catch((err) => {
    //handle error
  });
  //Optional
  .finally(() => {
    //Do something after the promise is resolved or rejected
  });
```

## Query Utility

- _Use for fix TYPES of API calls_

```js
const queryUtility = {
  async Load(url) {
    return await Query(url, "GET");
  },
  async Save(url, data) {
    return await Query(url, "POST", data);
  },
  async Update(url, data) {
    return await Query(url, "PUT", data);
  },
  async Delete(url, data) {
    return await Query(url, "DELETE", data);
  },
};
```

## Usage

### Arrow Function

```js
const loadTable = async () => {
  try {
    const response = await queryUtility.Load("/api");
    //Do something with the response like loadTable() or populateTable()
  } catch (err) {
    console.log(err);
  }
};

loadTable();
```

### Function

```js
async function updateStatus(data) {
  try {
    const response = await queryUtility.Update("/api", data);
    //Do something with the response
    //like if(response.msg === 'success'){ SWAL call }
  } catch (err) {
    console.log(err);
  }
}

//eventListener
$(document).on("click", "#button", function () {
  updateStatus();
});
```
