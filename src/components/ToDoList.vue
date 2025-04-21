<template>
    <section class="todo-app">
        <div class="container py-5">
            <div class="row d-flex justify-content-center">
                <div class="col-lg-8 col-md-10">
                    <div class="card main-card">
                        <div class="card-body p-4 p-md-5">
                            
                            <div class="header-section text-center mb-4">
                                <h1 class="app-title">
                                    <i class="fas fa-check-circle"></i>
                                    <span>Elegant Tasks</span>
                                </h1>
                                <p class="app-subtitle">Manage your tasks with style</p>
                            </div>

                            <div class="input-section">
                                <div class="card input-card">
                                    <div class="card-body">
                                        <div class="d-flex align-items-center input-container">
                                            <i class="fas fa-tasks task-icon"></i>
                                            <input type="text" 
                                                v-model="newTask" 
                                                class="form-control task-input"
                                                placeholder="What would you like to accomplish?"
                                                @keyup.enter="add()">
                                            <button type="button" 
                                                @click="add()" 
                                                class="btn action-btn add-btn">
                                                {{ btn }}
                                            </button>
                                        </div>
                                    </div>
                                </div>
                            </div>

                            <div class="filter-section d-flex justify-content-between align-items-center my-4">
                                <div class="task-counter">
                                    <span>{{ activeTasksCount }} active tasks</span>
                                </div>
                                <div class="filter-controls d-flex align-items-center">
                                    <span class="filter-label me-2">View:</span>
                                    <select class="form-select filter-select" v-model="filter">
                                        <option value="1">All Tasks</option>
                                        <option value="2">Completed</option>
                                        <option value="3">Active</option>
                                    </select>
                                </div>
                            </div>

                            <div class="task-list-container">
                                <transition-group name="task-list" tag="div">
                                    <!-- All Tasks -->
                                    <div v-if="filter==1" key="all-tasks" class="task-group">
                                        <div v-for="task in tasks" :key="task.id" class="task-item" :class="{'task-completed': task.checked === 1}">
                                            <div class="task-content">
                                                <div class="form-check">
                                                    <input class="form-check-input custom-checkbox" 
                                                        type="checkbox" 
                                                        @click="check(task.id)" 
                                                        v-model="checkTasks" 
                                                        :value="task.id"
                                                        :checked="task.checked" />
                                                </div>
                                                <p class="task-text">{{ task.content }}</p>
                                            </div>
                                            <div class="task-actions">
                                                <button @click="Edit(task.id)" class="btn action-btn edit-btn" title="Edit task">
                                                    <i class="fas fa-pen"></i>
                                                </button>
                                                <button @click="Delete(task.id)" class="btn action-btn delete-btn" title="Delete task">
                                                    <i class="fas fa-trash"></i>
                                                </button>
                                            </div>
                                        </div>
                                        <div v-if="tasks.length === 0" class="empty-state">
                                            <i class="fas fa-clipboard-list empty-icon"></i>
                                            <p>Your task list is empty</p>
                                        </div>
                                    </div>

                                    <!-- Completed Tasks -->
                                    <div v-else-if="filter==2" key="completed-tasks" class="task-group">
                                        <div v-for="task in completedTasks" :key="task.id" class="task-item task-completed">
                                            <div class="task-content">
                                                <div class="form-check">
                                                    <input class="form-check-input custom-checkbox" 
                                                        type="checkbox" 
                                                        @click="check(task.id)" 
                                                        v-model="checkTasks" 
                                                        :value="task.id"
                                                        :checked="task.checked" />
                                                </div>
                                                <p class="task-text">{{ task.content }}</p>
                                            </div>
                                            <div class="task-actions">
                                                <button @click="Edit(task.id)" class="btn action-btn edit-btn" title="Edit task">
                                                    <i class="fas fa-pen"></i>
                                                </button>
                                                <button @click="Delete(task.id)" class="btn action-btn delete-btn" title="Delete task">
                                                    <i class="fas fa-trash"></i>
                                                </button>
                                            </div>
                                        </div>
                                        <div v-if="completedTasks.length === 0" class="empty-state">
                                            <i class="fas fa-check-double empty-icon"></i>
                                            <p>No completed tasks</p>
                                        </div>
                                    </div>

                                    <!-- Active Tasks -->
                                    <div v-else-if="filter==3" key="active-tasks" class="task-group">
                                        <div v-for="task in activeTasks" :key="task.id" class="task-item">
                                            <div class="task-content">
                                                <div class="form-check">
                                                    <input class="form-check-input custom-checkbox" 
                                                        type="checkbox" 
                                                        @click="check(task.id)" 
                                                        v-model="checkTasks" 
                                                        :value="task.id"
                                                        :checked="task.checked" />
                                                </div>
                                                <p class="task-text">{{ task.content }}</p>
                                            </div>
                                            <div class="task-actions">
                                                <button @click="Edit(task.id)" class="btn action-btn edit-btn" title="Edit task">
                                                    <i class="fas fa-pen"></i>
                                                </button>
                                                <button @click="Delete(task.id)" class="btn action-btn delete-btn" title="Delete task">
                                                    <i class="fas fa-trash"></i>
                                                </button>
                                            </div>
                                        </div>
                                        <div v-if="activeTasks.length === 0" class="empty-state">
                                            <i class="fas fa-tasks empty-icon"></i>
                                            <p>No active tasks</p>
                                        </div>
                                    </div>
                                </transition-group>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>
</template>
<script>

export default {
    name: 'TodoList',
    data() {
        return {
            btn : "Add",
            newTask : "",
            tasks: [],
            checkTasks:  [],
            taskIdUpdate : null,
            isActive :true,
            filter : 1,
        }
    },
    computed: {
        completedTasks() {
            return this.tasks.filter(task => task.checked === 1);
        },
        activeTasks() {
            return this.tasks.filter(task => task.checked === 0);
        },
        activeTasksCount() {
            return this.activeTasks.length;
        }
    },
    mounted() {
        if (localStorage.tasks) {
            this.tasks = JSON.parse(localStorage.tasks);
            this.tasks.forEach((task)=>{
                if(task.checked  === 1){
                    this.checkTasks.push(task.id);
                }
            })
        }
    },
    methods: {
        add(){
            if(!this.newTask.trim()) return;

            if(this.btn === "Add"){
                let lastID;
                if(this.tasks.length !== 0){
                    lastID = this.tasks[this.tasks.length - 1].id;
                }else{
                    lastID = 0;
                }
                this.tasks.unshift({
                    id : lastID + 1,
                    content: this.newTask,
                    checked: 0 
                });
                this.newTask = "";
                localStorage.tasks = JSON.stringify(this.tasks);
            }else{
                if(Number.isInteger(this.taskIdUpdate)){
                    this.tasks[this.taskIdUpdate].content = this.newTask;
                    this.taskIdUpdate = null;
                    this.btn = "Add";
                    this.newTask = "";
                    localStorage.tasks = JSON.stringify(this.tasks);
                }else{
                    this.taskIdUpdate = null;
                    this.newTask = "";
                    alert("Task not found");
                }
            }
        },
        Edit(key){
            let getTask = this.tasks.find((task)=>task.id === key);
            let index = this.tasks.findIndex((task)=>task.id === key);
            this.newTask = getTask.content;
            this.btn = "Update";
            this.taskIdUpdate = index;
        },
        Delete(key){
            let index = this.tasks.findIndex((task)=>task.id === key);
            this.tasks.splice(index,1);
            localStorage.tasks = JSON.stringify(this.tasks);
        },
        check(key){
            let index = this.tasks.findIndex((task)=>task.id === key);
            if(this.checkTasks.includes(key)){
                this.tasks[index].checked = 0;
            }else{
                this.tasks[index].checked = 1;
            }
            localStorage.tasks = JSON.stringify(this.tasks);
        }
    }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap');
@import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css');

/* General Styles */
.todo-app {
    font-family: 'Poppins', sans-serif;
    min-height: 100vh;
    background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
    display: flex;
    align-items: center;
}

.main-card {
    border: none;
    border-radius: 16px;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
    background-color: #fff;
    overflow: hidden;
}

/* Header Section */
.header-section {
    margin-bottom: 2rem;
}

.app-title {
    color: #5a67d8;
    font-weight: 700;
    margin-bottom: 0.5rem;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 12px;
}

.app-title i {
    font-size: 1.5rem;
}

.app-subtitle {
    color: #718096;
    font-weight: 400;
    font-size: 1rem;
    margin-top: 8px;
}

/* Input Section */
.input-card {
    border-radius: 12px;
    border: none;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
    margin-bottom: 1.5rem;
    transition: all 0.3s ease;
}

.input-card:hover {
    box-shadow: 0 6px 16px rgba(0, 0, 0, 0.1);
}

.input-container {
    position: relative;
}

.task-icon {
    position: absolute;
    left: 15px;
    color: #5a67d8;
    font-size: 1.2rem;
}

.task-input {
    border: none;
    box-shadow: none;
    padding-left: 45px;
    height: 50px;
    font-size: 1rem;
    transition: all 0.3s ease;
}

.task-input:focus {
    box-shadow: none;
    border-color: transparent;
}

.action-btn {
    border: none;
    transition: all 0.2s ease;
    border-radius: 8px;
}

.add-btn {
    background-color: #5a67d8;
    color: white;
    padding: 8px 20px;
    font-weight: 500;
}

.add-btn:hover {
    background-color: #4c51bf;
    transform: translateY(-1px);
}

/* Filter Section */
.filter-section {
    padding: 12px 0;
    border-top: 1px solid #e2e8f0;
    border-bottom: 1px solid #e2e8f0;
}

.task-counter {
    color: #718096;
    font-size: 0.9rem;
}

.filter-label {
    color: #718096;
    font-size: 0.9rem;
}

.filter-select {
    border-radius: 8px;
    border: 1px solid #e2e8f0;
    padding: 6px 12px;
    font-size: 0.9rem;
    width: auto;
    background-color: white;
}

/* Task List */
.task-list-container {
    margin-top: 1.5rem;
    max-height: 50vh;
    overflow-y: auto;
    padding-right: 5px;
}

.task-list-container::-webkit-scrollbar {
    width: 6px;
}

.task-list-container::-webkit-scrollbar-track {
    background: #f1f1f1;
    border-radius: 10px;
}

.task-list-container::-webkit-scrollbar-thumb {
    background: #cbd5e0;
    border-radius: 10px;
}

.task-list-container::-webkit-scrollbar-thumb:hover {
    background: #a0aec0;
}

.task-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 16px;
    background-color: white;
    border-radius: 12px;
    margin-bottom: 10px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
    transition: all 0.3s ease;
    border-left: 4px solid #5a67d8;
}

.task-item:hover {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    transform: translateY(-2px);
}

.task-completed {
    opacity: 0.7;
    border-left: 4px solid #68d391;
}

.task-completed .task-text {
    text-decoration: line-through;
    color: #a0aec0;
}

.task-content {
    display: flex;
    align-items: center;
    gap: 12px;
    flex: 1;
}

.task-text {
    margin: 0;
    font-size: 1rem;
    font-weight: 400;
    color: #4a5568;
}

.task-actions {
    display: flex;
    gap: 8px;
}

.edit-btn {
    background-color: #f6e05e;
    color: #744210;
    width: 36px;
    height: 36px;
    padding: 0;
    display: flex;
    align-items: center;
    justify-content: center;
}

.edit-btn:hover {
    background-color: #ecc94b;
}

.delete-btn {
    background-color: #fc8181;
    color: #742a2a;
    width: 36px;
    height: 36px;
    padding: 0;
    display: flex;
    align-items: center;
    justify-content: center;
}

.delete-btn:hover {
    background-color: #f56565;
}

.custom-checkbox {
    width: 20px;
    height: 20px;
    border-radius: 4px;
    cursor: pointer;
}

/* Empty State */
.empty-state {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 40px 0;
    color: #a0aec0;
    text-align: center;
}

.empty-icon {
    font-size: 3rem;
    margin-bottom: 1rem;
    opacity: 0.5;
}

/* Animations */
.task-list-enter-active, .task-list-leave-active {
    transition: all 0.5s;
}
.task-list-enter, .task-list-leave-to {
    opacity: 0;
    transform: translateY(30px);
}
</style>