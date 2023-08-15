<script setup>
import { ref, computed } from "vue";
import TodoCard from "../components/TodoCard.vue";
import FormInput from "../components/form/input.vue";
import { useForm } from "vee-validate";
import { object, string } from "yup";

const { handleSubmit, handleReset } = useForm({
    validationSchema: object().shape({
        todo: string()
            .required("Kolom ini harus diisi")
            .test("unique", "${path} sudah pernah diinput", (value) => {
                return !todos.value.map((t) => t.text).includes(value);
            }),
    }),
});

let id = 0;

const todos = ref([]);

const incompleteTodo = computed(() => {
    return todos.value.filter((x) => x.completed === false);
});

const completeTodo = computed(() => {
    return todos.value.filter((x) => x.completed === true);
});
// const onSubmit = () => {
// disini kita menggunakan "spread operator (...)"
// untuk melepas proxy dari fungsi ractive milik Vue
// supaya ketika value object didalamnya kita rubah dia tidak ikut berubah

// bisa di cek menggunakan perintah
// console.log(todo)
// untuk melihat indikator proxy

// tanda ... merupakan spread operator yang mengambil semua elemen yang ada ditaruh pada suatu objek maupun array

// todos.value = [{ ...todo }, ...todos.value];
// todo.id = id += 1;
// todo.text = "";
// todo.completed = false;

//     handleSubmit((values) => {
//         console.log(values);
//     });
// };

const onSubmit = handleSubmit((values) => {
    const newTodo = {
        id: ++id,
        text: values.todo,
        completed: false,
    };
    todos.value = [newTodo, ...todos.value];
    handleReset();
    console.log(values);
});

const onClick = (todoItem) => {
    todos.value = todos.value.filter((t) => t.id !== todoItem.id);
};

// const onClickDone = (index) => {
//     done.value.splice(index, 1);
// };

// const onBox = (index) => {
//     if (todo.value.done) {
//         return "text-decoration:line-through";
//     }
// };
// const onDone = (index) => {
//     const x = todos.value[index];
//     done.value = [x, ...done.value];
//     todos.value.splice(index, 1);
// };
</script>

<template>
    <h1 class="text-center">todo app</h1>
    <main class="x`text-black h-screen flex items-center justify-center">
        <div class="`">
            <form class="w-[400px] mb-5" @submit.prevent="onSubmit" novalidate>
                <FormInput name="todo" placeholder="Mau apa hari ini?" />
            </form>
            <div class="grid grid-cols-12 gap-4">
                <div class="col-span-6">
                    <TodoCard
                        v-for="todoItem in incompleteTodo"
                        :key="todoItem.id"
                        :todo="todoItem"
                        @remove="onClick"
                    />
                </div>
                <div class="col-span-6">
                    <TodoCard
                        v-for="todoItem in completeTodo"
                        :key="todoItem.id"
                        :todo="todoItem"
                        @remove="onClick"
                    />
                </div>
            </div>
        </div>
    </main>
</template>
