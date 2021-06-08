<template>
    <header class="text-center">
        <h1>{{ title }}</h1>
    </header>
</template>


<script>

export default {
    name: 'header',
    props: {
        title: String,
    },
    components: {
    }
}
</script>
