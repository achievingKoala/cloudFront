<template>
    <div>
        testGet
    </div>
</template>
<script>
import axios from 'axios';

export default {
    name: 'GetTest',
    mounted() {
        console.log("---")
        axios.get("https://service-h95mmpda-1301472420.gz.apigw.tencentcs.com/release/sample").then(
            response => {
                console.log(response)
            }
        ).catch(error => {
            console.log(error)
            }
        )
    }
}
</script>