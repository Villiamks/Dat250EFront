<script>
    let userid = null;
    let user = null;
    let username = "";
    let nyBruker = false;
    let users = [];
    let polls = [];
    
    getAllUsers();
    getAllPolls();
    CheckLoggedinn();

    if ( userid !== 0 && userid !== null && userid !== "null"){
        getUserById(userid);
    }

    // From API:

    async function getAllUsers(){
        await fetch("http://localhost:8080/api/users")
        .then(res => {
            return res.json()
        })
        .then(data =>  {
            users = data;
        })
        .catch(error => console.log(error))
    }
    async function getUserById(id){
        await fetch("http://localhost:8080/api/users/find?id=" + userid)
        .then(res => {
            return res.json();
        })
        .then(data => {
            user = data;
            username = data.username;
        })
        .catch(error => {
            localStorage.removeItem("userid");
        });
    }
    async function createUser(inData){
        await fetch("http://localhost:8080/api/users", {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(inData)
            })
            .then(response => {
                return response.json();
            })
            .then(data => {
                localStorage.setItem("userid", data.id);
                location.reload();
            })
            .catch(error => {
                console.error('Error:', error);
            });
    }

    async function getAllPolls(){
        await fetch("http://localhost:8080/api/polls")
        .then(res => {
            return res.json()
        })
        .then(data =>  {
            polls = data;
            console.log(polls);
        })
        .catch(error => console.log(error))
    }

    async function createPoll(inData){
        await fetch("http://localhost:8080/api/polls", {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(inData)
            })
            .then(response => {
                return response.json();
            })
            .then(data => {
                data;
                //location.reload();
            })
            .catch(error => {
                console.error('Error:', error);
            });
    }

    //Login and Register:

    function Login(){
        let name = document.getElementById("name").value;
        let password = document.getElementById("password").value;

        if (name == null || name == "" ){
            userid = 0;
        } else {
            for (let i = 0; i <= users.length; i++){
                let tmp = users[i];
                if (tmp != null && tmp.username == name && tmp.password == password){
                    userid = tmp.id;
                }
            }
        }
        localStorage.setItem("userid", userid);
        location.reload();
    }

    function LoggOut(){
        localStorage.removeItem("userid");
        location.reload();
    }

    function CheckLoggedinn(){
        let stored = localStorage.getItem("userid");
        
        if (stored !== "null" || stored !== null){
            if ( stored == "0"){
                username = "Anon";
            } else {
                userid = stored;
            }
        }else {
            userid = null;
        }
    }

    function toggleNyBruker(){
        nyBruker = !nyBruker;
    }

    function RegisterUser(){
        let name = document.getElementById("name").value;
        let email = document.getElementById("email").value;
        let password = document.getElementById("password").value;

        let inData = {
            username: name,
            email: email,
            password: password
        }
        createUser(inData);
    }

    //Polls:

    function RegisterPoll(){
        let caption = document.getElementById("captioninp").value;
        let options = document.getElementsByClassName("optioninp");


    }

    function AddPollOption(){
        let box = document.getElementById("newpollbox");
        box.insertAdjacentHTML("beforeend", "Option: <input type=Text class=optioninp /> <br>");
    }

</script>

<main>

    {#if nyBruker}
        <h1>Registerer</h1>
        <input type="text" id="name" placeholder="name"> <br>
        <input type="text" id="email" placeholder="email"> <br>
        <input type="password" id="password" placeholder="password"> <br>
        <button type="button" onclick={RegisterUser} >Registerer</button>
    {/if}

    {#if (userid === null || userid === "null") && !nyBruker}
        <h1>Login</h1>

        <input type="text" id="name" placeholder="name"> <br>
        <input type="password" id="password" placeholder="password"> <br>

        <button type="button" onclick={Login} >Login</button>
        <button type="button" onclick={toggleNyBruker} >Registerer ny</button>
    {/if}

    {#if (userid !== null && userid !== "null") }
        
        <h1> Hello {username}! 
            <button type="button" onclick={LoggOut} >Logg out</button>
        </h1>

        <div class="box">
            <div>Caption: <input type="text" id="captioninp"/> </div>
            <div id="newpollbox">
                Option: <input type="text" class="optioninp" /> <br>
            </div>
            <button type="button" onclick={AddPollOption} >Add Option</button>
            <button type="button" onclick={RegisterPoll} >Create Poll</button>
        </div>

        <div class="box" >

            <div>Poll##</div>
            <h2>Name of Poll</h2>
            <div>option1 <button>Vote</button> .. Votes </div>
            <div>option1 <button>Vote</button> .. Votes </div>

        </div>
    {/if}
</main>
