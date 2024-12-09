<template>
    <header class="py-3">
        <div class="container-xl d-flex justify-content-between align-items-center">
          <img src="/src/assets/images/logo.svg" alt="Logo" width="140" height="35" />

            <!-- Navbar -->
            <nav class="header__navbar row justify-content-between align-items-center">
                <ul class="list-menu" type="none">
                    <li>Home</li>
                    <li class="font-orange fw-bold">Order</li>
                    <li>About</li>
                    <li>Blog</li>
                    <li>Contact us</li>
                </ul>
            </nav>
            <div class="signup">
                <a href="" class="text-login mt-1">Login</a>
                <a href="" class="button-register" style="margin-top: -2px;">Register</a>
            </div>
        </div>
    </header>
</template>

<script>

</script>


<style scoped>

header {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    z-index: 10;
    background: transparent;
    padding: 15px 0;
}

.container-xl {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    align-items: center;
}


.list-menu ul {
    display: flex;
    gap: 15px;
    flex-wrap: wrap;
    padding: 0;
    margin: 0;
}

.list-menu li {
    list-style: none;
    cursor: pointer;
}

.signup {
    display: flex;
    gap: 10px;
}

@media (max-width: 768px) {
    .header__navbar {
        width: 100%;
    }
    .signup {
        margin-top: 10px;
        justify-content: center;
        width: 100%;
    }
    .list-menu ul {
        justify-content: center;
    }
}
</style>
