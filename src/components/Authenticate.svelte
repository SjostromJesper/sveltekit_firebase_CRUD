<script>
    import {authHandlers} from "../store/store.js";

    let email = ''
    let password = ''
    let confirmPassword = ''

    let error = false

    let register = false
    let authenticating = false

    const handleAuth = async () => {
        if(authenticating) return

        if (!email || !password || (register && !confirmPassword)) {
            error = true
            return
        }

        authenticating = true

        try {
            if (!register) {
                await authHandlers.login(email, password)
            } else {
                await authHandlers.signup(email, password)
            }
        } catch (error) {
            console.log("there was an auth error", error)
            error = true
        }

        authenticating = false
    }

    const handleRegister = () => {
        register = !register
    }
</script>

<div class="auth-container">
    <form action="">
        <h1>{register ? 'Register' : 'Login'} </h1>

        {#if error}
            <p class="error">The information you entered is not correct.</p>
        {/if}

        <label>
            <input bind:value={email} type="email" placeholder="email">
        </label>

        <label>
            <input bind:value={password} type="password" placeholder="password">
        </label>

        {#if register}
            <label>
                <input bind:value={confirmPassword} type="password" placeholder="Confirm Password">
            </label>
        {/if}
        <button type="button" on:click={handleAuth}>
            {#if authenticating}
                SPINNING
            {:else}
                Submit
            {/if}
        </button>
    </form>
    <div class="options">
        <p>Or</p>
        {#if register}
            <div>
                <p>Already have an account?</p>
                <p on:click={handleRegister} on:keydown={() => {}}>Login</p>
            </div>
        {:else}
            <div>
                <p>Don't have an account?</p>
                <p on:click={handleRegister} on:keydown={() => {}}>Register</p>
            </div>
        {/if}
    </div>
</div>

<style>
    .auth-container {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        flex: 1;
    }

    form, .options {
        width: 400px;
        max-width: 100%;
        margin: 0 auto;
    }

    form {
        display: flex;
        flex-direction: column;
        gap: 14px;
    }

    form input {
        width: 100%;

        border: none;
        background: transparent;
        color: white;
    }

    form label {
        position: relative;
        border: 1px solid dodgerblue;
        border-radius: 5px;
    }

    form input:focus {
        border: none;
        outline: none;
        padding: 8px 14px;
    }

    form label:focus-within {
        border-color: aqua;
    }

    h1 {
        text-align: center;
    }

    form button {
        background: blue;
        color: white;
        border: none;
        padding: 10px 0;
        border-radius: 5px;
        cursor: pointer;
    }

    form button:hover {
        background: navy;
    }

    .error {
        color: coral;
        font-size: 0.9rem;
    }

    .options {
        padding: 14px 0;
        overflow: hidden;
        width: 100%;
        font-size: 0.9rem;
        display: flex;
        flex-direction: column;
        gap: 8px;
    }

    .options > p {
        position: relative;
        text-align: center;
        width: fit-content;
        margin: 0 auto;
        padding: 0 8px;
    }

    .options > p::after,
    .options > p::before {
        position: absolute;
        content: '';
        top: 50%;
        transform: translateY(-50%);
        width: 100vw;
        height: 2px;
        background: white;
    }

    .options > p::after {
        right: 100%;
    }

    .options > p::before {
        left: 100%;
    }

    .options div {
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 8px;
    }

    .options div p:last-of-type {
        color: blue;
        cursor: pointer;
    }
</style>