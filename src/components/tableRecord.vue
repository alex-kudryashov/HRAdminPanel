<template>
    <tr :id="employee._id">
        
            <td><input type="text" v-model="employee.name" v-on:blur="alert(employee)"></td>
            <td><input type="text" v-model="employee.position"></td>
            <td><input type="text" v-model="employee.salary"></td>
            <td>
                <select name="status" v-model="employee.status">
                    <option value="1">Соискатель</option>
                    <option value="2">Уволен</option>
                    <option value="3">Сотрудник</option>
                </select>
            </td>
            <td><input type="date" v-model="employee.recruitmentDate"></td>
            <td><button v-on:click="$emit('remove-record',employee._id)">✖</button></td>
            <td><button v-on:click="$emit('update-record',employee._id)">✔</button></td>
        
    </tr>
</template>

<script>
    export default {
        props: {
            employee: {
                type: Object,
                required: true
            }
        }
    }
</script>

<style>

</style>