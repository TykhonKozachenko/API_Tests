# API Tests

### Conduit
- [JSON](https://github.com/TykhonKozachenko/API_Tests/blob/main/Conduit/Conduit.json)
- Access to the [collection](https://api.postman.com/collections/32217972-9a276d97-d344-4ac7-9ff9-8895475d31c2?access_key=PMAT-01HQGSX9QYJ4WWAYQHPRVG03S1) (via API)
- Access to the [environment](https://github.com/TykhonKozachenko/API_Tests/files/15010022/Conduit.postman_environment.json) (via JSON)

<br>

### Samples
<details>
<summary>Pre-request scripts</summary>
<br>
  
```
postman.setEnvironmentVariable('registration', ()=> {
  const url = pm.environment.get('url');
  const username = pm.variables.replaceIn('{{$randomUserName}}') + Math.floor(Math.random(1000) * 1000);
  const email = pm.variables.replaceIn('{{$randomEmail}}');

  const registrationRequest = {
    url: url + 'users',
    method: 'POST',
    header: {
      'Content-Type': 'application/json',
    },
    body: {
      mode: 'raw',
      raw: JSON.stringify({
        "user": {
          "username": username,
          "email": email,
          "password": "Qwert123!_"
        }
      })
    }
  };

  pm.sendRequest(registrationRequest, (error, response) => {
    console.log(error ? error : response.json());
    pm.environment.set("token", response.json().user.token);
    pm.environment.set("email", response.json().user.email);
    pm.environment.set("username", response.json().user.username);
  });
});

postman.setEnvironmentVariable('articleCreation', ()=> {
  const url = pm.environment.get('url');
  const token = pm.environment.get('token');

  const createArticle = {
    url: url + 'articles',
    method: 'POST',
    header: {
      'Content-Type': 'application/json',
      'Authorization': 'Token ' + token,
    },
    body: {
      mode: 'raw',
      raw: JSON.stringify({
        "article": {
          "title": "article title",
          "description": "description",
          "body": "hello world",
          "tagList": [],
        }
      })
    }
  };

  pm.sendRequest(createArticle, (error, response) => {
    console.log(error ? error : response.json());
    pm.environment.set('slug', response.json().article.slug);
  });
});

postman.setEnvironmentVariable('articleDeletion', ()=> {
  const url = pm.environment.get('url');
  const slug = pm.environment.get('slug');
  const token = pm.environment.get('token');

  const deleteArticle = {
    url: url + 'articles/' + slug,
    method: 'DELETE',
    header: {
      'Content-Type': 'application/json',
      'Authorization': 'Token ' + token,
    },
    body: {
      mode: 'none',
    }
  };

  pm.sendRequest(deleteArticle, (error, response) => {
    console.log(error ? error : response.json());
  });
});

postman.setEnvironmentVariable('commentPost', ()=> {
  const url = pm.environment.get('url');
  const slug = pm.environment.get('slug');
  const token = pm.environment.get('token');
  const id  =  pm.environment.get('commentID');

  const commentPost = {
    url: url + 'articles/' + slug +  '/comments',
    method: 'POST',
    header: {
      'Content-Type': 'application/json',
      'Authorization': 'Token ' + token,
    },
    body: {
      mode: 'raw',
      raw: JSON.stringify({
        "comment": {
          "body": "cool comment",
        }
      })
    }
  };

  pm.sendRequest(commentPost, (error, response) => {
    console.log(error ? error : response.json());
    pm.environment.set('commentID', response.json().comment.id);
  });
});

postman.setEnvironmentVariable('randomUsername', ()=> {
  const randomUsernamePrefix = pm.variables.replaceIn('{{$randomUserName}}');
  const randomUsernameSuffix = Math.floor(Math.random() * 1000);
  const randomUsername = randomUsernamePrefix.replace(/\./g, '') + randomUsernameSuffix;

  pm.environment.set('randomUsername', randomUsername.toLowerCase());
});

```
</details>

<details>
<summary>Tests</summary>
<br>
  
```
postman.setGlobalVariable('OK', ()=> {
  pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
  });
  pm.test("Response time is less than 1000ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(1000);
  });
});

postman.setGlobalVariable('UnprocessableEntity', ()=> {
  pm.test("Status code is 422", function () {
    pm.response.to.have.status(422);
  });
  pm.test("Response time is less than 1000ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(1000);
  });
});

postman.setGlobalVariable('userIsRegistered', ()=> {
  pm.test("All response properties are correct", () => {
    pm.response.to.have.jsonBody('user.username')
      .and.to.have.jsonBody('user.email')
      .and.to.have.jsonBody('user.token')
      .and.to.have.jsonBody('user.bio')
      .and.to.have.jsonBody('user.image');
  });
});

postman.setGlobalVariable('articleIsCreated', ()=> {
  pm.test("All article response properties are correct", () => {
    pm.response.to.have.jsonBody('article.slug')
      .and.to.have.jsonBody('article.title')
      .and.to.have.jsonBody('article.description')
      .and.to.have.jsonBody('article.body')
      .and.to.have.jsonBody('article.createdAt')
      .and.to.have.jsonBody('article.updatedAt')
      .and.to.have.jsonBody('article.tagList')
      .and.to.have.jsonBody('article.favorited')
      .and.to.have.jsonBody('article.favoritesCount')
      .and.to.have.jsonBody('article.author.username')
      .and.to.have.jsonBody('article.author.bio')
      .and.to.have.jsonBody('article.author.image')
      .and.to.have.jsonBody('article.author.following')
  });
});


```
</details>
