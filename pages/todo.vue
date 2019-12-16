<template>
<div>
<label v-for="label in options">
        <input type="radio"
            v-model="current"
            v-bind:value="label.value">{{ label.label}}
    </label>
    <table>
      <thead>
        <tr>
          <th class="id">ID</th>
          <th class="comment">コメント</th>
          <th class="state">状態</th>
          <th class="button">-</th>
        </tr>
      </thead>
      <tbody>
          <tr v-for="item in computedTodos" v-bind:key="item.id">
            <th>{{ item.id }}</th>
            <td>{{ item.comment }}</td>
            <td class="state">
                <!-- 状態変更ボタンのモック -->
                <button v-on:click="doChangeStage(item)">{{ labels[item.state] }}</button>
            </td>
            <td class="button">
                <!-- 削除ボタンのモック -->
                <button v-on:click="doRemove(item)">削除</button>
            </td>
              </tr>
      </tbody>
    </table>
    <h2>新しい作業の追加</h2>
    <form class="add-form" v-on:submit.prevent="doAdd">
        <!-- コメントの入力フォーム -->
        コメント<input type="text" ref="comment">
        <!-- 追加ボタンのモック -->
        <button type="submit">追加</button>
    </form>
    </div>
</template>

<script>

var STORAGE_KEY = 'todos-vuejs-demo'
var todoStorage = {
  fetch: function () {
    var todos = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]')
    todos.forEach(function (todo, index) {
      todo.id = index
    })
    todoStorage.uid = todos.length
    return todos
  },
  save: function (todos) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos))
  }
}

export default {
    data() {
        return {
            todos: [],
            current: -1,
            options: [
                { value: -1, label: 'すべて'},
                { value: 0, label: '作業中'},
                { value: 1, label: '完了'}
            ]
        };
    },
    computed: {
        computedTodos: function() {
            return this.todos.filter(function (el) {
                return this.current < 0 ? true : this.current === el.state
            }, this);
        },
        labels() {
            return this.options.reduce(function(a, b) {
                return Object.assign(a, { [b.value]: b.label });
            }, {});
        }
    },
    watch: {
        // todosは監視するデータ
        todos: {
            handler: function(todos) {
                console.log("ああああああああ");
                todoStorage.save(todos);
            },
            deep: true
        }
    },
    created() {
        this.todos = todoStorage.fetch();
        console.log(this.labels);
    },
    methods: {
        doAdd(event, value) {
            var comment = this.$refs.comment;
            if (!comment.value.length) {
                return
            }
            this.todos.push({
                id: todoStorage.uid++,
                comment: comment.value,
                state: 0
            });
            console.log(this.todos);
            comment.value = ''
            // todoStorage.save(this.todos);
        },
        doChangeStage(item) {
            item.state = !item.state ? 1 : 0
        },
        doRemove(item) {
            var index = this.todos.indexOf(item);
            this.todos.splice(index, 1)
            console.log(item);
        }
    },
};
</script>

<style scoped>
</style>