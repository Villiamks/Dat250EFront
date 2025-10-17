
<script>
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
</script>

<h1>Registerer</h1>
<input type="text" id="name" placeholder="name"> <br>
<input type="text" id="email" placeholder="email"> <br>
<input type="password" id="password" placeholder="password"> <br>
<button type="button" onclick={RegisterUser} >Registerer</button>
