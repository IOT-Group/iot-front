<template>
    <div style="width:2rem;height:2rem">
        <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="100%" height="100%">
            <circle cx="1rem" cy="1rem" r=".65rem" stroke="black"
                stroke-width="4" :fill="state==0?'red':'#28a745'"/>
        </svg> 
    </div>   
</template>

<script>
export default {
    name:"Indicator",
    props:{
        state:{
            default:0
        }
    }
}
</script>