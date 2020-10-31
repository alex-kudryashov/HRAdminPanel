<template>
      <table>
          <thead>
            <tr>
                <th width="250">
                    ФИО
                </th>
                <th width="200">
                    Должность
                </th>
                <th width="100">
                    Оклад
                </th>
                <th width="150">
                    Статус      
                </th>
                <th width="150">
                    Дата приёма на работу
                </th>
                <th>
                    Удалить
                </th>
                <th>
                    Сохранить
                </th>
            </tr>
        </thead>
        <tbody>
            <tableRecord 
                v-for="employee of employees"
                v-bind:employee="employee"
                v-bind:key="employee._id"
                @remove-record="removeRecord"  
                @update-record="updateRecord"  
            />
        </tbody>
      </table>
</template>

<script>

    import tableRecord from "@/components/tableRecord"
    export default {
        props: ['employees'],
        components: {
            tableRecord
        },
        methods: {
            removeRecord(id) {
                this.$emit('remove-record', id)
            },
            updateRecord(id) {
                this.$emit('update-record', id)
            }
        }
    }

</script>

<style>

</style>