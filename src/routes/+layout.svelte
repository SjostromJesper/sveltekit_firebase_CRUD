<script>
    import {onMount} from "svelte";
    import {auth, db} from "$lib/firebase/firebase.js";
    import {doc, getDoc, setDoc} from 'firebase/firestore'
    import {authStore} from "../store/store.js";

    const nonAuthRoutes = ['/']

    onMount(() => {
        console.log('Mounting')
        const unsubscribe = auth.onAuthStateChanged(async user => {
            const currentPath = window.location.pathname

            if (!user && !nonAuthRoutes.includes(currentPath)) {
                window.location.href = "/"
                return
            }

            if (user && currentPath === '/') {
                window.location.href = '/dashboard'
                return
            }

            if(!user) return

            let dataToSetToStore;

            const docRef = doc(db, "users", user.uid)
            const docSnap = await getDoc(docRef)

            if (!docSnap.exists()) {
                const userRef = doc(db, 'users', user.uid)

                dataToSetToStore = {
                    email: user.email,
                    todos: []
                }

                await setDoc(userRef, dataToSetToStore,
                    {merge: true})
            }
            else {
                dataToSetToStore = docSnap.data();
            }
            authStore.update((curr => {
                return {
                    ...curr,
                    user,
                    data: dataToSetToStore,
                    loading: false
                }
            }))
        })
    })
</script>

<div class="main-container">
    <slot/>
</div>

<style>
    .main-container {
        min-height: 100vh;
        background: linear-gradient(to right, dodgerblue, salmon);
        color: white;

        position: relative;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
    }
</style>