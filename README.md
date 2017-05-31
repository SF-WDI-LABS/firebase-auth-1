# Firebase Authentication

We will be re-building the infamous growler app, this time with firebase and react.JS . Instead of using Auth0
for authentication, we will use Firebases build in authentication. We will use react-router

Firebase has Oauth, and standard authentication built in. Signing up or logging in is as easy as calling a method.
However, it will be our responsibility to persist the user on the frontend.

**(Tutorialspoint Demo)**

**Codealong Steps**
1. Git Pull from WDI-10

2. Create OAuth Application on Github

3. Log in to my firebase and show the process

4. Go through the mockups

5. create-react-app inside student/instructor directory

6. Delete boilerplate code, create components from mockup, bring over css

``` javascript
// Login.js
import '../bower_components/bootstrap/dist/css/bootstrap.css';
import '../bower_components/font-awesome/css/font-awesome.css';
import '../bower_components/bootstrap-social/bootstrap-social.css';
```

7. Setup react-router-dom inside of index.js, with the Login component & app component

``` javascript
import { BrowserRouter as Router, Route } from 'react-router-dom';

<Router>
    <div>
      <Route exact path="/" component={Login}/>
      <Route path="/app" component={App} />
      <Route path="*" render={()=>{
        return <Redirect to="/" />
      }} />
    </div>
  </Router>
```

8. Build authentication functions inside login.js

``` javascript
googleSignIn(){
    var provider = new firebase.auth.GoogleAuthProvider();
    firebase.auth().signInWithPopup(provider).then((result) => {
      var token = result.credential.accessToken;
      var user = result.user;
   }).catch((error) => {
      var errorCode = error.code;
      var errorMessage = error.message;
   });
  }

  githubSignIn(){
    const provider = new firebase.auth.GithubAuthProvider();
    firebase.auth().signInWithPopup(provider).then((result) => {
        var token = result.credential.accessToken;
        var user = result.user;
     }).catch((error) => {
        var errorCode = error.code;
        var errorMessage = error.message;
     });
  }
```

9. create onClick for auth buttons, get a console.log of the information

10. create localPersist to add localstorage and redirect to app component

11. create addGrowl, showGrowls, and persistGrowls methods:

``` javascript 
persistGrowls(){
    this.dbRef.set({
      growls: this.state.growls
    });
  }

  componentDidMount(){
    this.showGrowls()
  }

  showGrowls(){
    this.dbRef.on('value',(snapshot)=>{
      const db = snapshot.val();
      // console.log('db: ', db)

      if(db){
        this.setState({growls: db.growls})
      }
    });
  }

  addGrowl(msg){

    const growls = this.state.growls;
    const user = JSON.parse(localStorage.getItem('user'));

    growls.unshift({
      name: user.displayName,
      email: user.email,
      pic: user.photoURL,
      msg: msg,
      id: Date.now()
    })

    this.setState({growls})
    console.log('state: ', this.state.growls)
    this.persistGrowls()
  }
```

12. Make AddGrowl a controlled component, and pass it addGrowl method as a prop.

13. When form submits call a method that calles e.preventDefault(), and this.props.addGrowl, and pass it the message.

14. Check out firebase and console.logs and make sure that growls are being added

15. map growls down to GrowlList, and finally growl.

``` javascript
// GrowlList.js
const growls = this.props.growls.map(growl => {
  return(
    <Growl key={growl.id} name={growl.name} email={growl.email} pic={growl.pic} msg={growl.msg}/>
  )
});
```

16. add Signout method and map it to signout button
``` javascript
// App.js
signout() {
    firebase.auth().signOut().then(() => {
      console.log('Signout Succesfull');
      localStorage.removeItem('user');
      this.setState({ loggedIn: false })
    }, function(error) {
      console.error('Signout Failed')
    });
}
```

17. add this.state.loggedIn, and then a redirect if it is false

**Finish up**

