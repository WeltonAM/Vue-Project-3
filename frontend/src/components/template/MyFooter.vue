<template>
  <footer class="footer">
    <span>
        Copyright ©
        <strong>
            <a href="https://github.com/WeltonAM" target="_black">WeltonMatosDev</a>
        </strong>
        <span> {{ new Date().getFullYear() }} | </span>
    </span> 

    <span> 
        | At <strong>
            <a href="https://www.cod3r.com.br/" target="_black"> Cod3r</a> 
        </strong>
    </span>
  
  </footer>
</template>

<script>

export default {
    name: 'MyFooter',
}
</script>

<style>
    .footer{
        grid-area: footer;
        background-color: #DDD;
        color: #333;

        display: flex;
        align-items: center;
        justify-content: flex-end;
        padding-right: 10px;
    }

    .footer span a {
        text-decoration: none;
        color: #333;
    }

    .footer span a:visited{
        color: #333;
    }
</style>