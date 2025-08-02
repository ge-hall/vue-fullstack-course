# Module 8: Advanced Frontend Features

**Duration:** 4-5 days  
**Branch:** `feature/module-8-advanced-frontend`  
**Section:** 🚀 **ADVANCED**

## Learning Objectives
- Implement real-time features with WebSockets
- Build advanced state management with Pinia
- Create file upload and media handling systems
- Develop drag-and-drop interfaces for task management
- Implement performance optimization techniques
- Build Progressive Web App (PWA) features

## Overview
This module transforms your application into a modern, interactive experience with real-time updates, advanced UI patterns, and performance optimizations. You'll implement features that make your application feel native and responsive, rivaling commercial task management platforms.

## Tasks

### Task 8.1: Advanced State Management with Pinia
**Objective:** Implement comprehensive state management for complex application state

**What you need to accomplish:**
- Set up Pinia stores for different domain areas
- Implement state persistence and hydration
- Create reactive state patterns for real-time updates
- Build optimistic UI updates

**Documentation to consult:**
- [Pinia Documentation](https://pinia.vuejs.org/)
- [Pinia with Nuxt](https://pinia.vuejs.org/ssr/nuxt.html)
- [State Management Patterns](https://pinia.vuejs.org/core-concepts/state.html)

**Pinia store structure:**
```typescript
// stores/auth.ts
export const useAuthStore = defineStore('auth', () => {
  const user = ref<User | null>(null)
  const session = ref<Session | null>(null)
  const isAuthenticated = computed(() => !!user.value)
  
  const setUser = (userData: User | null) => {
    user.value = userData
  }
  
  const setSession = (sessionData: Session | null) => {
    session.value = sessionData
  }
  
  const logout = async () => {
    try {
      await $fetch('/api/auth/logout', { method: 'POST' })
      user.value = null
      session.value = null
      await navigateTo('/login')
    } catch (error) {
      console.error('Logout error:', error)
    }
  }
  
  return {
    user: readonly(user),
    session: readonly(session),
    isAuthenticated,
    setUser,
    setSession,
    logout
  }
}, {
  persist: {
    storage: persistedState.localStorage
  }
})
```

**Projects store with optimistic updates:**
```typescript
// stores/projects.ts
export const useProjectsStore = defineStore('projects', () => {
  const projects = ref<Project[]>([])
  const currentProject = ref<Project | null>(null)
  const loading = ref(false)
  const error = ref<string | null>(null)
  
  // Getters
  const activeProjects = computed(() => 
    projects.value.filter(p => !p.isArchived)
  )
  
  const getProjectById = computed(() => 
    (id: string) => projects.value.find(p => p.id === id)
  )
  
  // Actions
  const fetchProjects = async () => {
    loading.value = true
    error.value = null
    
    try {
      const response = await $fetch<ApiResponse<{ projects: Project[] }>>('/api/projects')
      projects.value = response.data.projects
    } catch (err: any) {
      error.value = err.message
      throw err
    } finally {
      loading.value = false
    }
  }
  
  const createProject = async (projectData: NewProject) => {
    // Optimistic update
    const tempProject: Project = {
      id: `temp-${Date.now()}`,
      ...projectData,
      createdAt: new Date(),
      updatedAt: new Date()
    }
    
    projects.value.unshift(tempProject)
    
    try {
      const response = await $fetch<ApiResponse<{ project: Project }>>('/api/projects', {
        method: 'POST',
        body: projectData
      })
      
      // Replace temporary project with real one
      const index = projects.value.findIndex(p => p.id === tempProject.id)
      if (index !== -1) {
        projects.value[index] = response.data.project
      }
      
      return response.data.project
    } catch (error) {
      // Rollback on error
      const index = projects.value.findIndex(p => p.id === tempProject.id)
      if (index !== -1) {
        projects.value.splice(index, 1)
      }
      throw error
    }
  }
  
  const updateProject = async (id: string, updates: Partial<Project>) => {
    const originalProject = projects.value.find(p => p.id === id)
    if (!originalProject) return
    
    // Optimistic update
    const index = projects.value.findIndex(p => p.id === id)
    projects.value[index] = { ...originalProject, ...updates }
    
    try {
      const response = await $fetch<ApiResponse<{ project: Project }>>(`/api/projects/${id}`, {
        method: 'PATCH',
        body: updates
      })
      
      projects.value[index] = response.data.project
      return response.data.project
    } catch (error) {
      // Rollback on error
      projects.value[index] = originalProject
      throw error
    }
  }
  
  const deleteProject = async (id: string) => {
    const index = projects.value.findIndex(p => p.id === id)
    if (index === -1) return
    
    const deletedProject = projects.value[index]
    
    // Optimistic removal
    projects.value.splice(index, 1)
    
    try {
      await $fetch(`/api/projects/${id}`, {
        method: 'DELETE'
      })
    } catch (error) {
      // Rollback on error
      projects.value.splice(index, 0, deletedProject)
      throw error
    }
  }
  
  return {
    projects: readonly(projects),
    currentProject: readonly(currentProject),
    loading: readonly(loading),
    error: readonly(error),
    activeProjects,
    getProjectById,
    fetchProjects,
    createProject,
    updateProject,
    deleteProject
  }
})
```

**Tasks store with real-time updates:**
```typescript
// stores/tasks.ts
export const useTasksStore = defineStore('tasks', () => {
  const tasks = ref<Map<string, Task[]>>(new Map()) // projectId -> tasks
  const loading = ref(false)
  const error = ref<string | null>(null)
  
  // WebSocket connection for real-time updates
  const ws = ref<WebSocket | null>(null)
  
  const getTasksByProject = computed(() => 
    (projectId: string) => tasks.value.get(projectId) || []
  )
  
  const getTasksByStatus = computed(() => 
    (projectId: string, status: string) => 
      getTasksByProject.value(projectId).filter(t => t.status === status)
  )
  
  const fetchTasks = async (projectId: string) => {
    loading.value = true
    error.value = null
    
    try {
      const response = await $fetch<ApiResponse<{ tasks: Task[] }>>(`/api/projects/${projectId}/tasks`)
      tasks.value.set(projectId, response.data.tasks)
    } catch (err: any) {
      error.value = err.message
      throw err
    } finally {
      loading.value = false
    }
  }
  
  const createTask = async (projectId: string, taskData: NewTask) => {
    const currentTasks = tasks.value.get(projectId) || []
    
    // Optimistic update
    const tempTask: Task = {
      id: `temp-${Date.now()}`,
      ...taskData,
      projectId,
      createdAt: new Date(),
      updatedAt: new Date()
    }
    
    tasks.value.set(projectId, [tempTask, ...currentTasks])
    
    try {
      const response = await $fetch<ApiResponse<{ task: Task }>>(`/api/projects/${projectId}/tasks`, {
        method: 'POST',
        body: taskData
      })
      
      // Replace temporary task
      const updatedTasks = tasks.value.get(projectId) || []
      const index = updatedTasks.findIndex(t => t.id === tempTask.id)
      if (index !== -1) {
        updatedTasks[index] = response.data.task
        tasks.value.set(projectId, [...updatedTasks])
      }
      
      return response.data.task
    } catch (error) {
      // Rollback
      const updatedTasks = tasks.value.get(projectId) || []
      const index = updatedTasks.findIndex(t => t.id === tempTask.id)
      if (index !== -1) {
        updatedTasks.splice(index, 1)
        tasks.value.set(projectId, [...updatedTasks])
      }
      throw error
    }
  }
  
  const updateTaskStatus = async (taskId: string, status: string) => {
    const projectId = findTaskProject(taskId)
    if (!projectId) return
    
    const currentTasks = tasks.value.get(projectId) || []
    const taskIndex = currentTasks.findIndex(t => t.id === taskId)
    if (taskIndex === -1) return
    
    const originalTask = currentTasks[taskIndex]
    
    // Optimistic update
    const updatedTask = { 
      ...originalTask, 
      status,
      updatedAt: new Date(),
      ...(status === 'completed' ? { completedAt: new Date() } : {})
    }
    
    currentTasks[taskIndex] = updatedTask
    tasks.value.set(projectId, [...currentTasks])
    
    try {
      await $fetch(`/api/tasks/${taskId}`, {
        method: 'PATCH',
        body: { status }
      })
    } catch (error) {
      // Rollback
      currentTasks[taskIndex] = originalTask
      tasks.value.set(projectId, [...currentTasks])
      throw error
    }
  }
  
  const findTaskProject = (taskId: string): string | null => {
    for (const [projectId, projectTasks] of tasks.value.entries()) {
      if (projectTasks.some(t => t.id === taskId)) {
        return projectId
      }
    }
    return null
  }
  
  // Real-time updates
  const connectWebSocket = () => {
    if (process.client && !ws.value) {
      ws.value = new WebSocket(`ws://localhost:3001/ws`)
      
      ws.value.onmessage = (event) => {
        const data = JSON.parse(event.data)
        handleRealTimeUpdate(data)
      }
    }
  }
  
  const handleRealTimeUpdate = (data: any) => {
    switch (data.type) {
      case 'task_created':
        addTaskToStore(data.task)
        break
      case 'task_updated':
        updateTaskInStore(data.task)
        break
      case 'task_deleted':
        removeTaskFromStore(data.taskId, data.projectId)
        break
    }
  }
  
  return {
    tasks: readonly(tasks),
    loading: readonly(loading),
    error: readonly(error),
    getTasksByProject,
    getTasksByStatus,
    fetchTasks,
    createTask,
    updateTaskStatus,
    connectWebSocket
  }
})
```

**Acceptance criteria:**
- [ ] Pinia stores implemented for all domain areas
- [ ] State persistence working
- [ ] Optimistic UI updates functional
- [ ] Reactive state patterns implemented
- [ ] Error handling and rollback working

---

### Task 8.2: Real-Time Features with WebSockets
**Objective:** Implement real-time collaboration features using WebSockets

**What you need to accomplish:**
- Set up WebSocket server in Nuxt
- Implement real-time task updates
- Create live collaboration indicators
- Build real-time notifications system

**Documentation to consult:**
- [Nuxt WebSocket Integration](https://nuxt.com/docs/guide/directory-structure/server#websockets)
- [WebSocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)
- [Socket.io Documentation](https://socket.io/docs/v4/)

**WebSocket server setup:**
```typescript
// server/api/websocket.ts
import { Server } from 'socket.io'
import type { Server as HTTPServer } from 'http'

let io: Server

export default defineEventHandler(async (event) => {
  if (!io) {
    // @ts-ignore
    const httpServer: HTTPServer = event.node.req.socket?.server
    
    io = new Server(httpServer, {
      cors: {
        origin: process.env.CLIENT_URL || "http://localhost:3000",
        methods: ["GET", "POST"]
      }
    })
    
    // Handle connections
    io.on('connection', (socket) => {
      console.log('User connected:', socket.id)
      
      // Join project rooms
      socket.on('join_project', (projectId: string) => {
        socket.join(`project:${projectId}`)
        console.log(`User ${socket.id} joined project ${projectId}`)
      })
      
      // Leave project rooms
      socket.on('leave_project', (projectId: string) => {
        socket.leave(`project:${projectId}`)
        console.log(`User ${socket.id} left project ${projectId}`)
      })
      
      // Handle task updates
      socket.on('task_update', (data) => {
        // Broadcast to all users in the project
        socket.to(`project:${data.projectId}`).emit('task_updated', data)
      })
      
      // Handle user typing
      socket.on('user_typing', (data) => {
        socket.to(`project:${data.projectId}`).emit('user_typing', {
          userId: data.userId,
          taskId: data.taskId,
          isTyping: data.isTyping
        })
      })
      
      // Handle cursor position (for collaborative editing)
      socket.on('cursor_move', (data) => {
        socket.to(`project:${data.projectId}`).emit('cursor_moved', {
          userId: data.userId,
          position: data.position,
          taskId: data.taskId
        })
      })
      
      socket.on('disconnect', () => {
        console.log('User disconnected:', socket.id)
      })
    })
  }
  
  return 'WebSocket server initialized'
})
```

**Real-time composable:**
```typescript
// composables/useRealTime.ts
export const useRealTime = () => {
  const socket = ref<any>(null)
  const isConnected = ref(false)
  const onlineUsers = ref<Map<string, any>>(new Map())
  
  const connect = () => {
    if (process.client && !socket.value) {
      // Use socket.io-client for better reliability
      socket.value = io('ws://localhost:3001')
      
      socket.value.on('connect', () => {
        isConnected.value = true
        console.log('Connected to WebSocket server')
      })
      
      socket.value.on('disconnect', () => {
        isConnected.value = false
        console.log('Disconnected from WebSocket server')
      })
    }
  }
  
  const disconnect = () => {
    if (socket.value) {
      socket.value.disconnect()
      socket.value = null
      isConnected.value = false
    }
  }
  
  const joinProject = (projectId: string) => {
    if (socket.value && isConnected.value) {
      socket.value.emit('join_project', projectId)
    }
  }
  
  const leaveProject = (projectId: string) => {
    if (socket.value && isConnected.value) {
      socket.value.emit('leave_project', projectId)
    }
  }
  
  const emitTaskUpdate = (projectId: string, task: Task) => {
    if (socket.value && isConnected.value) {
      socket.value.emit('task_update', {
        projectId,
        task,
        timestamp: new Date()
      })
    }
  }
  
  const onTaskUpdate = (callback: (data: any) => void) => {
    if (socket.value) {
      socket.value.on('task_updated', callback)
    }
  }
  
  const emitUserTyping = (projectId: string, taskId: string, isTyping: boolean) => {
    if (socket.value && isConnected.value) {
      socket.value.emit('user_typing', {
        projectId,
        taskId,
        isTyping,
        userId: useAuthStore().user?.id
      })
    }
  }
  
  const onUserTyping = (callback: (data: any) => void) => {
    if (socket.value) {
      socket.value.on('user_typing', callback)
    }
  }
  
  return {
    isConnected: readonly(isConnected),
    onlineUsers: readonly(onlineUsers),
    connect,
    disconnect,
    joinProject,
    leaveProject,
    emitTaskUpdate,
    onTaskUpdate,
    emitUserTyping,
    onUserTyping
  }
}
```

**Real-time task component:**
```vue
<!-- components/Tasks/TaskCard.vue -->
<template>
  <div 
    class="bg-white border rounded-lg p-4 shadow-sm hover:shadow-md transition-shadow"
    :class="{
      'ring-2 ring-blue-500': isBeingEdited,
      'opacity-75': isOptimistic
    }"
  >
    <div class="flex items-start justify-between">
      <div class="flex-1">
        <h3 
          class="font-medium text-gray-900"
          @click="startEditing"
        >
          {{ task.title }}
        </h3>
        
        <p v-if="task.description" class="text-sm text-gray-600 mt-1">
          {{ task.description }}
        </p>
        
        <div class="flex items-center mt-3 space-x-2">
          <select 
            v-model="localStatus"
            @change="updateStatus"
            class="text-xs border rounded px-2 py-1"
          >
            <option value="todo">To Do</option>
            <option value="in_progress">In Progress</option>
            <option value="completed">Completed</option>
          </select>
          
          <span 
            v-if="task.priority"
            class="text-xs px-2 py-1 rounded"
            :class="priorityClasses[task.priority]"
          >
            {{ task.priority }}
          </span>
        </div>
      </div>
      
      <div class="flex flex-col items-end space-y-2">
        <!-- Online users working on this task -->
        <div v-if="activeUsers.length > 0" class="flex -space-x-1">
          <img
            v-for="user in activeUsers"
            :key="user.id"
            :src="user.avatar || '/default-avatar.png'"
            :alt="user.name"
            class="w-6 h-6 rounded-full border-2 border-white"
            :title="`${user.name} is ${user.isTyping ? 'typing' : 'viewing'}`"
          />
        </div>
        
        <!-- Typing indicator -->
        <div v-if="someoneTyping" class="text-xs text-gray-500 flex items-center">
          <div class="flex space-x-1 mr-1">
            <div class="w-1 h-1 bg-gray-400 rounded-full animate-bounce"></div>
            <div class="w-1 h-1 bg-gray-400 rounded-full animate-bounce" style="animation-delay: 0.1s"></div>
            <div class="w-1 h-1 bg-gray-400 rounded-full animate-bounce" style="animation-delay: 0.2s"></div>
          </div>
          typing...
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
interface Props {
  task: Task
  projectId: string
}

const props = defineProps<Props>()

const { emitTaskUpdate, onTaskUpdate, emitUserTyping, onUserTyping } = useRealTime()
const { updateTaskStatus } = useTasksStore()

const localStatus = ref(props.task.status)
const isBeingEdited = ref(false)
const activeUsers = ref<any[]>([])
const someoneTyping = ref(false)

const isOptimistic = computed(() => 
  props.task.id.startsWith('temp-')
)

const priorityClasses = {
  low: 'bg-green-100 text-green-800',
  medium: 'bg-yellow-100 text-yellow-800',
  high: 'bg-red-100 text-red-800'
}

const updateStatus = async () => {
  try {
    await updateTaskStatus(props.task.id, localStatus.value)
    
    // Emit real-time update
    emitTaskUpdate(props.projectId, {
      ...props.task,
      status: localStatus.value
    })
  } catch (error) {
    // Revert on error
    localStatus.value = props.task.status
  }
}

const startEditing = () => {
  isBeingEdited.value = true
  emitUserTyping(props.projectId, props.task.id, true)
  
  // Stop typing after 3 seconds of inactivity
  setTimeout(() => {
    isBeingEdited.value = false
    emitUserTyping(props.projectId, props.task.id, false)
  }, 3000)
}

// Listen for real-time updates
onMounted(() => {
  onTaskUpdate((data) => {
    if (data.task.id === props.task.id) {
      localStatus.value = data.task.status
    }
  })
  
  onUserTyping((data) => {
    if (data.taskId === props.task.id) {
      // Update typing indicators
      if (data.isTyping) {
        someoneTyping.value = true
      } else {
        someoneTyping.value = false
      }
    }
  })
})

watch(() => props.task.status, (newStatus) => {
  localStatus.value = newStatus
})
</script>
```

**Acceptance criteria:**
- [ ] WebSocket server implemented
- [ ] Real-time task updates working
- [ ] Live collaboration indicators functional
- [ ] Typing indicators working
- [ ] Real-time notifications system implemented

---

### Task 8.3: Drag and Drop Interface
**Objective:** Create an intuitive drag-and-drop interface for task management

**What you need to accomplish:**
- Implement drag-and-drop for task status changes
- Create sortable task lists
- Add visual feedback during drag operations
- Handle drag-and-drop across different projects

**Documentation to consult:**
- [Vue Draggable](https://github.com/SortableJS/vue.draggable.next)
- [HTML Drag and Drop API](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API)
- [SortableJS Documentation](https://sortablejs.github.io/Sortable/)

**Install dependencies:**
```bash
npm install vuedraggable-es
```

**Kanban board component:**
```vue
<!-- components/Projects/KanbanBoard.vue -->
<template>
  <div class="flex space-x-6 overflow-x-auto pb-6">
    <div 
      v-for="status in taskStatuses"
      :key="status.value"
      class="flex-shrink-0 w-80"
    >
      <div class="bg-gray-50 rounded-lg p-4">
        <div class="flex items-center justify-between mb-4">
          <h3 class="font-semibold text-gray-700">
            {{ status.label }}
          </h3>
          <span class="bg-gray-200 text-gray-600 text-xs px-2 py-1 rounded-full">
            {{ getTasksByStatus(status.value).length }}
          </span>
        </div>
        
        <draggable
          :list="getTasksByStatus(status.value)"
          group="tasks"
          item-key="id"
          :animation="200"
          ghost-class="opacity-50"
          chosen-class="ring-2 ring-blue-500"
          drag-class="rotate-3 shadow-xl"
          @change="handleDragChange"
          @start="onDragStart"
          @end="onDragEnd"
          class="space-y-3 min-h-[200px]"
        >
          <template #item="{ element: task }">
            <TaskCard
              :task="task"
              :project-id="projectId"
              :is-dragging="draggingTaskId === task.id"
              @task-updated="handleTaskUpdate"
            />
          </template>
          
          <!-- Drop zone indicator -->
          <template #footer>
            <div 
              v-if="isDragging && dragOverStatus === status.value"
              class="h-2 bg-blue-200 border-2 border-dashed border-blue-400 rounded"
            />
          </template>
        </draggable>
        
        <!-- Add task button -->
        <button
          @click="showAddTask(status.value)"
          class="w-full mt-3 p-3 border-2 border-dashed border-gray-300 rounded-lg text-gray-500 hover:border-gray-400 hover:text-gray-600 transition-colors"
        >
          <Plus class="w-4 h-4 inline mr-2" />
          Add task
        </button>
      </div>
    </div>
  </div>
  
  <!-- Add task modal -->
  <TaskModal
    v-if="showModal"
    :project-id="projectId"
    :initial-status="selectedStatus"
    @close="showModal = false"
    @task-created="handleTaskCreated"
  />
</template>

<script setup lang="ts">
import draggable from 'vuedraggable-es'

interface Props {
  projectId: string
}

const props = defineProps<Props>()

const { getTasksByStatus, updateTaskStatus } = useTasksStore()
const { emitTaskUpdate } = useRealTime()

const taskStatuses = [
  { value: 'todo', label: 'To Do' },
  { value: 'in_progress', label: 'In Progress' },
  { value: 'completed', label: 'Completed' }
]

const isDragging = ref(false)
const draggingTaskId = ref<string | null>(null)
const dragOverStatus = ref<string | null>(null)
const showModal = ref(false)
const selectedStatus = ref('todo')

const handleDragChange = async (event: any) => {
  if (event.moved) {
    const { element: task, newIndex, oldIndex } = event.moved
    
    // Update task position
    await updateTaskPosition(task.id, newIndex)
  }
  
  if (event.added) {
    const { element: task, newIndex } = event.added
    const newStatus = findStatusFromIndex(newIndex)
    
    if (newStatus && task.status !== newStatus) {
      try {
        await updateTaskStatus(task.id, newStatus)
        
        // Emit real-time update
        emitTaskUpdate(props.projectId, {
          ...task,
          status: newStatus
        })
        
        // Show success feedback
        showSuccessToast(`Task moved to ${newStatus.replace('_', ' ')}`)
      } catch (error) {
        console.error('Error updating task status:', error)
        showErrorToast('Failed to update task')
      }
    }
  }
}

const findStatusFromIndex = (index: number): string | null => {
  // Logic to determine which status column based on drop position
  return dragOverStatus.value
}

const onDragStart = (event: any) => {
  isDragging.value = true
  draggingTaskId.value = event.item.dataset.taskId
  
  // Add visual feedback
  document.body.classList.add('dragging')
}

const onDragEnd = (event: any) => {
  isDragging.value = false
  draggingTaskId.value = null
  dragOverStatus.value = null
  
  // Remove visual feedback
  document.body.classList.remove('dragging')
}

const updateTaskPosition = async (taskId: string, newPosition: number) => {
  try {
    await $fetch(`/api/tasks/${taskId}`, {
      method: 'PATCH',
      body: { position: newPosition }
    })
  } catch (error) {
    console.error('Error updating task position:', error)
  }
}

const showAddTask = (status: string) => {
  selectedStatus.value = status
  showModal.value = true
}

const handleTaskCreated = (task: Task) => {
  showModal.value = false
  // Task will be added to store by the modal component
}

const handleTaskUpdate = (task: Task) => {
  // Handle individual task updates
  emitTaskUpdate(props.projectId, task)
}

// Add keyboard shortcuts
onMounted(() => {
  document.addEventListener('keydown', handleKeyboard)
})

onUnmounted(() => {
  document.removeEventListener('keydown', handleKeyboard)
})

const handleKeyboard = (event: KeyboardEvent) => {
  if (event.key === 'Escape' && isDragging.value) {
    // Cancel drag operation
    isDragging.value = false
    draggingTaskId.value = null
  }
}
</script>

<style scoped>
.dragging {
  @apply cursor-grabbing;
}

.drag-item {
  @apply cursor-grab;
}

.drag-item:hover {
  @apply shadow-md;
}

.sortable-ghost {
  @apply opacity-50;
}

.sortable-chosen {
  @apply ring-2 ring-blue-500;
}

.sortable-drag {
  @apply rotate-3 shadow-xl;
}
</style>
```

**Enhanced task card with drag handle:**
```vue
<!-- components/Tasks/TaskCard.vue (enhanced) -->
<template>
  <div 
    :data-task-id="task.id"
    class="bg-white border rounded-lg shadow-sm hover:shadow-md transition-all duration-200 cursor-pointer"
    :class="{
      'ring-2 ring-blue-500': isBeingEdited,
      'opacity-75': isOptimistic,
      'transform rotate-2 shadow-xl': isDragging
    }"
  >
    <!-- Drag handle -->
    <div class="flex items-center">
      <div class="flex-shrink-0 p-2 cursor-grab hover:bg-gray-50 rounded-l-lg">
        <svg class="w-4 h-4 text-gray-400" fill="currentColor" viewBox="0 0 20 20">
          <path d="M7 2a2 2 0 1 1-4 0 2 2 0 0 1 4 0zM7 8a2 2 0 1 1-4 0 2 2 0 0 1 4 0zM7 14a2 2 0 1 1-4 0 2 2 0 0 1 4 0zM17 2a2 2 0 1 1-4 0 2 2 0 0 1 4 0zM17 8a2 2 0 1 1-4 0 2 2 0 0 1 4 0zM17 14a2 2 0 1 1-4 0 2 2 0 0 1 4 0z"></path>
        </svg>
      </div>
      
      <!-- Task content -->
      <div class="flex-1 p-4">
        <div class="flex items-start justify-between">
          <div class="flex-1">
            <h3 
              class="font-medium text-gray-900 hover:text-blue-600 transition-colors"
              @click="openTaskDetail"
            >
              {{ task.title }}
            </h3>
            
            <p v-if="task.description" class="text-sm text-gray-600 mt-1 line-clamp-2">
              {{ task.description }}
            </p>
            
            <!-- Task metadata -->
            <div class="flex items-center mt-3 space-x-2">
              <span 
                v-if="task.priority"
                class="text-xs px-2 py-1 rounded"
                :class="priorityClasses[task.priority]"
              >
                {{ task.priority }}
              </span>
              
              <span v-if="task.dueDate" class="text-xs text-gray-500">
                Due {{ formatDate(task.dueDate) }}
              </span>
              
              <span v-if="task.tags?.length" class="text-xs text-blue-600">
                {{ task.tags.join(', ') }}
              </span>
            </div>
          </div>
          
          <!-- Task actions -->
          <div class="flex flex-col items-end space-y-2 ml-4">
            <!-- Assignee avatar -->
            <img
              v-if="task.assignee"
              :src="task.assignee.avatar || '/default-avatar.png'"
              :alt="task.assignee.name"
              class="w-6 h-6 rounded-full"
              :title="task.assignee.name"
            />
            
            <!-- Online indicators -->
            <div v-if="activeUsers.length > 0" class="flex -space-x-1">
              <img
                v-for="user in activeUsers"
                :key="user.id"
                :src="user.avatar || '/default-avatar.png'"
                :alt="user.name"
                class="w-4 h-4 rounded-full border border-white"
                :title="`${user.name} is viewing`"
              />
            </div>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Progress bar for subtasks -->
    <div v-if="task.subtasks?.length" class="px-4 pb-3">
      <div class="flex items-center justify-between text-xs text-gray-500 mb-1">
        <span>Subtasks</span>
        <span>{{ completedSubtasks }}/{{ task.subtasks.length }}</span>
      </div>
      <div class="w-full bg-gray-200 rounded-full h-1">
        <div 
          class="bg-blue-600 h-1 rounded-full transition-all duration-300"
          :style="{ width: `${subtaskProgress}%` }"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
interface Props {
  task: Task
  projectId: string
  isDragging?: boolean
}

const props = defineProps<Props>()

const activeUsers = ref<any[]>([])
const isBeingEdited = ref(false)

const isOptimistic = computed(() => 
  props.task.id.startsWith('temp-')
)

const completedSubtasks = computed(() => 
  props.task.subtasks?.filter(st => st.completed).length || 0
)

const subtaskProgress = computed(() => {
  if (!props.task.subtasks?.length) return 0
  return (completedSubtasks.value / props.task.subtasks.length) * 100
})

const priorityClasses = {
  low: 'bg-green-100 text-green-800',
  medium: 'bg-yellow-100 text-yellow-800',
  high: 'bg-red-100 text-red-800'
}

const formatDate = (date: string | Date) => {
  return new Intl.DateTimeFormat('en-US', {
    month: 'short',
    day: 'numeric'
  }).format(new Date(date))
}

const openTaskDetail = () => {
  // Navigate to task detail or open modal
  navigateTo(`/projects/${props.projectId}/tasks/${props.task.id}`)
}
</script>
```

**Acceptance criteria:**
- [ ] Drag-and-drop functionality working smoothly
- [ ] Visual feedback during drag operations
- [ ] Task status updates on drop
- [ ] Sortable task lists implemented
- [ ] Real-time updates for drag operations

---

### Task 8.4: File Upload and Media Handling
**Objective:** Implement comprehensive file upload and media management system

**What you need to accomplish:**
- Create file upload component with drag-and-drop
- Implement file type validation and size limits
- Set up file storage (local or cloud)
- Build file preview and management interface

**Documentation to consult:**
- [Nuxt 3 File Uploads](https://nuxt.com/docs/guide/directory-structure/server#handling-multipart-form-data)
- [Cloudinary Integration](https://cloudinary.com/documentation/nuxt_integration)
- [File API](https://developer.mozilla.org/en-US/docs/Web/API/File)

**File upload composable:**
```typescript
// composables/useFileUpload.ts
export const useFileUpload = () => {
  const uploading = ref(false)
  const uploadProgress = ref(0)
  const uploadedFiles = ref<UploadedFile[]>([])
  
  const allowedTypes = [
    'image/jpeg', 'image/png', 'image/gif', 'image/webp',
    'application/pdf',
    'application/msword', 'application/vnd.openxmlformats-officedocument.wordprocessingml.document',
    'text/plain', 'text/csv'
  ]
  
  const maxFileSize = 10 * 1024 * 1024 // 10MB
  
  const validateFile = (file: File): string | null => {
    if (!allowedTypes.includes(file.type)) {
      return 'File type not supported'
    }
    
    if (file.size > maxFileSize) {
      return 'File size too large (max 10MB)'
    }
    
    return null
  }
  
  const uploadFile = async (file: File, context?: { taskId?: string, projectId?: string }) => {
    const error = validateFile(file)
    if (error) {
      throw new Error(error)
    }
    
    uploading.value = true
    uploadProgress.value = 0
    
    try {
      const formData = new FormData()
      formData.append('file', file)
      if (context?.taskId) formData.append('taskId', context.taskId)
      if (context?.projectId) formData.append('projectId', context.projectId)
      
      const response = await $fetch<{ data: UploadedFile }>('/api/upload', {
        method: 'POST',
        body: formData,
        onUploadProgress: (progress) => {
          uploadProgress.value = Math.round((progress.loaded / progress.total) * 100)
        }
      })
      
      const uploadedFile = response.data
      uploadedFiles.value.push(uploadedFile)
      
      return uploadedFile
    } catch (error) {
      throw error
    } finally {
      uploading.value = false
      uploadProgress.value = 0
    }
  }
  
  const uploadMultiple = async (files: FileList, context?: { taskId?: string, projectId?: string }) => {
    const results = []
    
    for (const file of Array.from(files)) {
      try {
        const result = await uploadFile(file, context)
        results.push(result)
      } catch (error) {
        console.error(`Failed to upload ${file.name}:`, error)
        results.push({ error: error.message, file: file.name })
      }
    }
    
    return results
  }
  
  const deleteFile = async (fileId: string) => {
    try {
      await $fetch(`/api/upload/${fileId}`, {
        method: 'DELETE'
      })
      
      uploadedFiles.value = uploadedFiles.value.filter(f => f.id !== fileId)
    } catch (error) {
      throw error
    }
  }
  
  return {
    uploading: readonly(uploading),
    uploadProgress: readonly(uploadProgress),
    uploadedFiles: readonly(uploadedFiles),
    uploadFile,
    uploadMultiple,
    deleteFile,
    validateFile
  }
}
```

**File upload server API:**
```typescript
// server/api/upload.post.ts
import { writeFile, mkdir } from 'fs/promises'
import { join } from 'path'
import { v4 as uuidv4 } from 'uuid'

export default defineEventHandler(async (event) => {
  try {
    const form = await readMultipartFormData(event)
    
    if (!form || !form.length) {
      throw createError({
        statusCode: 400,
        statusMessage: 'No files uploaded'
      })
    }
    
    const fileData = form.find(item => item.name === 'file')
    const taskId = form.find(item => item.name === 'taskId')?.data?.toString()
    const projectId = form.find(item => item.name === 'projectId')?.data?.toString()
    
    if (!fileData) {
      throw createError({
        statusCode: 400,
        statusMessage: 'File data not found'
      })
    }
    
    // Generate unique filename
    const fileId = uuidv4()
    const originalName = fileData.filename || 'unnamed'
    const fileExtension = originalName.split('.').pop()
    const fileName = `${fileId}.${fileExtension}`
    
    // Create upload directory if it doesn't exist
    const uploadDir = join(process.cwd(), 'uploads')
    await mkdir(uploadDir, { recursive: true })
    
    // Write file to disk
    const filePath = join(uploadDir, fileName)
    await writeFile(filePath, fileData.data)
    
    // Create file record in database
    const fileRecord = {
      id: fileId,
      originalName,
      fileName,
      filePath: `/uploads/${fileName}`,
      mimeType: fileData.type || 'application/octet-stream',
      size: fileData.data.length,
      taskId,
      projectId,
      uploadedAt: new Date()
    }
    
    // Save to database (implement based on your schema)
    // await fileOps.create(fileRecord)
    
    return {
      success: true,
      data: fileRecord
    }
  } catch (error) {
    throw createError({
      statusCode: error.statusCode || 500,
      statusMessage: error.statusMessage || 'Upload failed'
    })
  }
})
```

**File upload component:**
```vue
<!-- components/Files/FileUpload.vue -->
<template>
  <div class="space-y-4">
    <!-- Drop zone -->
    <div
      @drop="handleDrop"
      @dragover="handleDragOver"
      @dragenter="handleDragEnter"
      @dragleave="handleDragLeave"
      class="border-2 border-dashed rounded-lg p-8 text-center transition-colors"
      :class="{
        'border-blue-500 bg-blue-50': isDragOver,
        'border-gray-300': !isDragOver
      }"
    >
      <input
        ref="fileInput"
        type="file"
        multiple
        :accept="acceptedTypes.join(',')"
        @change="handleFileSelect"
        class="hidden"
      />
      
      <div v-if="!uploading">
        <svg class="w-12 h-12 text-gray-400 mx-auto mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"></path>
        </svg>
        
        <p class="text-lg font-medium text-gray-900 mb-2">
          Drop files here or click to browse
        </p>
        
        <p class="text-sm text-gray-500">
          Supports images, documents, and PDFs up to 10MB
        </p>
        
        <BaseButton
          @click="openFileDialog"
          variant="outline"
          class="mt-4"
        >
          Choose Files
        </BaseButton>
      </div>
      
      <!-- Upload progress -->
      <div v-else class="space-y-4">
        <div class="w-12 h-12 mx-auto">
          <div class="animate-spin rounded-full h-12 w-12 border-b-2 border-blue-600"></div>
        </div>
        
        <p class="text-lg font-medium text-gray-900">
          Uploading files...
        </p>
        
        <div class="w-full bg-gray-200 rounded-full h-2">
          <div 
            class="bg-blue-600 h-2 rounded-full transition-all duration-300"
            :style="{ width: `${uploadProgress}%` }"
          />
        </div>
        
        <p class="text-sm text-gray-500">
          {{ uploadProgress }}% complete
        </p>
      </div>
    </div>
    
    <!-- File list -->
    <div v-if="files.length > 0" class="space-y-2">
      <h4 class="font-medium text-gray-900">Uploaded Files</h4>
      
      <div class="grid gap-2">
        <FileItem
          v-for="file in files"
          :key="file.id"
          :file="file"
          @delete="handleDelete"
          @preview="handlePreview"
        />
      </div>
    </div>
    
    <!-- Error messages -->
    <div v-if="errors.length > 0" class="space-y-2">
      <div
        v-for="error in errors"
        :key="error.id"
        class="p-3 bg-red-50 border border-red-200 rounded text-red-700 text-sm"
      >
        {{ error.message }}
      </div>
    </div>
  </div>
  
  <!-- File preview modal -->
  <FilePreviewModal
    v-if="previewFile"
    :file="previewFile"
    @close="previewFile = null"
  />
</template>

<script setup lang="ts">
interface Props {
  taskId?: string
  projectId?: string
  existingFiles?: UploadedFile[]
}

const props = defineProps<Props>()
const emit = defineEmits<{
  filesUploaded: [files: UploadedFile[]]
  fileDeleted: [fileId: string]
}>()

const { uploadMultiple, deleteFile, uploading, uploadProgress } = useFileUpload()

const fileInput = ref<HTMLInputElement>()
const isDragOver = ref(false)
const files = ref<UploadedFile[]>(props.existingFiles || [])
const errors = ref<{ id: string, message: string }[]>([])
const previewFile = ref<UploadedFile | null>(null)

const acceptedTypes = [
  'image/*',
  'application/pdf',
  '.doc,.docx',
  '.txt,.csv'
]

const openFileDialog = () => {
  fileInput.value?.click()
}

const handleFileSelect = (event: Event) => {
  const target = event.target as HTMLInputElement
  if (target.files) {
    uploadFiles(target.files)
  }
}

const handleDrop = (event: DragEvent) => {
  event.preventDefault()
  isDragOver.value = false
  
  if (event.dataTransfer?.files) {
    uploadFiles(event.dataTransfer.files)
  }
}

const handleDragOver = (event: DragEvent) => {
  event.preventDefault()
}

const handleDragEnter = (event: DragEvent) => {
  event.preventDefault()
  isDragOver.value = true
}

const handleDragLeave = (event: DragEvent) => {
  event.preventDefault()
  isDragOver.value = false
}

const uploadFiles = async (fileList: FileList) => {
  errors.value = []
  
  try {
    const results = await uploadMultiple(fileList, {
      taskId: props.taskId,
      projectId: props.projectId
    })
    
    const successfulUploads = results.filter(r => !r.error)
    const failedUploads = results.filter(r => r.error)
    
    files.value.push(...successfulUploads)
    
    // Show errors for failed uploads
    failedUploads.forEach(failure => {
      errors.value.push({
        id: Math.random().toString(),
        message: `${failure.file}: ${failure.error}`
      })
    })
    
    emit('filesUploaded', successfulUploads)
  } catch (error) {
    errors.value.push({
      id: Math.random().toString(),
      message: error.message || 'Upload failed'
    })
  }
}

const handleDelete = async (fileId: string) => {
  try {
    await deleteFile(fileId)
    files.value = files.value.filter(f => f.id !== fileId)
    emit('fileDeleted', fileId)
  } catch (error) {
    errors.value.push({
      id: Math.random().toString(),
      message: 'Failed to delete file'
    })
  }
}

const handlePreview = (file: UploadedFile) => {
  previewFile.value = file
}

// Clear errors after 5 seconds
watch(errors, () => {
  if (errors.value.length > 0) {
    setTimeout(() => {
      errors.value = []
    }, 5000)
  }
})
</script>
```

**Acceptance criteria:**
- [ ] File upload with drag-and-drop working
- [ ] File type and size validation implemented
- [ ] File storage system functional
- [ ] File preview and management interface
- [ ] Error handling and progress feedback

---

### Task 8.5: Performance Optimization and PWA Features
**Objective:** Optimize application performance and add Progressive Web App capabilities

**What you need to accomplish:**
- Implement lazy loading and code splitting
- Add service worker for offline functionality
- Create app installation prompts
- Optimize bundle size and loading performance

**Documentation to consult:**
- [Nuxt 3 Performance](https://nuxt.com/docs/guide/concepts/rendering#performance)
- [PWA Module](https://pwa.nuxtjs.org/)
- [Web Vitals](https://web.dev/vitals/)

**PWA configuration:**
```bash
# Install PWA module
npm install @nuxtjs/pwa
```

```typescript
// nuxt.config.ts (updated)
export default defineNuxtConfig({
  modules: [
    '@nuxtjs/tailwindcss',
    '@pinia/nuxt',
    '@better-auth/nuxt',
    '@nuxtjs/pwa'
  ],
  
  pwa: {
    meta: {
      title: 'Task Manager',
      author: 'Your Name',
      description: 'Modern task management application',
      theme_color: '#3b82f6',
      background_color: '#ffffff'
    },
    
    manifest: {
      name: 'Task Manager',
      short_name: 'TaskMgr',
      description: 'Collaborative task management made simple',
      theme_color: '#3b82f6',
      background_color: '#ffffff',
      display: 'standalone',
      orientation: 'portrait',
      start_url: '/',
      icons: [
        {
          src: '/icon-192x192.png',
          sizes: '192x192',
          type: 'image/png'
        },
        {
          src: '/icon-512x512.png',
          sizes: '512x512',
          type: 'image/png'
        }
      ]
    },
    
    workbox: {
      enabled: true,
      offline: true,
      cacheAssets: true,
      preCaching: ['/'],
      runtimeCaching: [
        {
          urlPattern: '/api/.*',
          handler: 'NetworkFirst',
          options: {
            cacheName: 'api-cache',
            expiration: {
              maxEntries: 100,
              maxAgeSeconds: 60 * 60 * 24 // 24 hours
            }
          }
        },
        {
          urlPattern: '/uploads/.*',
          handler: 'CacheFirst',
          options: {
            cacheName: 'media-cache',
            expiration: {
              maxEntries: 200,
              maxAgeSeconds: 60 * 60 * 24 * 30 // 30 days
            }
          }
        }
      ]
    }
  },
  
  // Performance optimizations
  experimental: {
    payloadExtraction: false // Better for large SPAs
  },
  
  nitro: {
    compressPublicAssets: true,
    minify: true
  },
  
  // Bundle analysis
  build: {
    analyze: process.env.ANALYZE === 'true'
  }
})
```

**Performance monitoring composable:**
```typescript
// composables/usePerformance.ts
export const usePerformance = () => {
  const metrics = ref({
    loading: false,
    renderTime: 0,
    apiResponseTime: 0,
    memoryUsage: 0
  })
  
  const measureRender = (startTime: number) => {
    const endTime = performance.now()
    metrics.value.renderTime = endTime - startTime
  }
  
  const measureApiCall = async <T>(apiCall: () => Promise<T>): Promise<T> => {
    const startTime = performance.now()
    
    try {
      const result = await apiCall()
      const endTime = performance.now()
      metrics.value.apiResponseTime = endTime - startTime
      return result
    } catch (error) {
      throw error
    }
  }
  
  const getMemoryUsage = () => {
    if ('memory' in performance) {
      // @ts-ignore
      const memory = performance.memory
      metrics.value.memoryUsage = memory.usedJSHeapSize / 1024 / 1024 // MB
    }
  }
  
  const reportWebVitals = () => {
    if (process.client) {
      // Report Core Web Vitals
      import('web-vitals').then(({ getCLS, getFID, getFCP, getLCP, getTTFB }) => {
        getCLS(console.log)
        getFID(console.log)
        getFCP(console.log)
        getLCP(console.log)
        getTTFB(console.log)
      })
    }
  }
  
  return {
    metrics: readonly(metrics),
    measureRender,
    measureApiCall,
    getMemoryUsage,
    reportWebVitals
  }
}
```

**Lazy loading components:**
```typescript
// components/LazyComponents.ts
export const LazyKanbanBoard = defineAsyncComponent(() => 
  import('~/components/Projects/KanbanBoard.vue')
)

export const LazyFileUpload = defineAsyncComponent(() => 
  import('~/components/Files/FileUpload.vue')
)

export const LazyChartDashboard = defineAsyncComponent(() => 
  import('~/components/Dashboard/ChartDashboard.vue')
)
```

**Optimized image component:**
```vue
<!-- components/Base/OptimizedImage.vue -->
<template>
  <div class="relative" :style="containerStyle">
    <img
      v-if="shouldLoad"
      :src="optimizedSrc"
      :alt="alt"
      :class="imageClass"
      @load="onLoad"
      @error="onError"
      loading="lazy"
    />
    
    <!-- Placeholder -->
    <div
      v-else
      class="absolute inset-0 bg-gray-200 animate-pulse rounded"
      :class="placeholderClass"
    />
    
    <!-- Loading state -->
    <div
      v-if="loading"
      class="absolute inset-0 flex items-center justify-center bg-gray-100"
    >
      <div class="animate-spin rounded-full h-8 w-8 border-b-2 border-blue-600" />
    </div>
  </div>
</template>

<script setup lang="ts">
interface Props {
  src: string
  alt: string
  width?: number
  height?: number
  quality?: number
  lazy?: boolean
  class?: string
}

const props = withDefaults(defineProps<Props>(), {
  quality: 80,
  lazy: true
})

const loading = ref(false)
const loaded = ref(false)
const error = ref(false)
const shouldLoad = ref(!props.lazy)

const containerStyle = computed(() => ({
  width: props.width ? `${props.width}px` : 'auto',
  height: props.height ? `${props.height}px` : 'auto'
}))

const optimizedSrc = computed(() => {
  // In production, you might use a service like Cloudinary
  // For now, return the original src
  return props.src
})

const imageClass = computed(() => [
  props.class,
  {
    'opacity-0': loading.value,
    'opacity-100': loaded.value,
    'transition-opacity duration-300': true
  }
])

const onLoad = () => {
  loading.value = false
  loaded.value = true
}

const onError = () => {
  loading.value = false
  error.value = true
}

// Intersection Observer for lazy loading
onMounted(() => {
  if (props.lazy && process.client) {
    const observer = new IntersectionObserver(
      (entries) => {
        if (entries[0].isIntersecting) {
          shouldLoad.value = true
          loading.value = true
          observer.disconnect()
        }
      },
      { threshold: 0.1 }
    )
    
    const element = getCurrentInstance()?.vnode.el as Element
    if (element) {
      observer.observe(element)
    }
  }
})
</script>
```

**Bundle analysis script:**
```json
{
  "scripts": {
    "analyze": "ANALYZE=true nuxt build",
    "lighthouse": "lighthouse http://localhost:3000 --output=html --output-path=./lighthouse-report.html"
  }
}
```

**Acceptance criteria:**
- [ ] PWA features implemented and working
- [ ] Service worker caching functional
- [ ] Lazy loading components working
- [ ] Performance monitoring implemented
- [ ] Bundle size optimized
- [ ] Core Web Vitals measured

## Challenge Extensions

### Advanced Real-Time Features
- Implement collaborative cursor tracking
- Add real-time voice/video calls
- Create live screen sharing
- Build real-time document editing

### Enhanced Performance
- Implement virtual scrolling for large lists
- Add intelligent prefetching
- Create custom service worker strategies
- Implement edge-side rendering

### Advanced PWA Features
- Add background sync for offline actions
- Implement push notifications
- Create app shortcuts and widgets
- Add share target functionality

### Analytics and Monitoring
- Implement user behavior tracking
- Add error reporting and monitoring
- Create performance dashboards
- Set up A/B testing framework

## Module Completion Checklist

Before moving to Module 9, ensure:
- [ ] Advanced state management working
- [ ] Real-time features functional
- [ ] Drag-and-drop interface implemented
- [ ] File upload system working
- [ ] PWA features enabled
- [ ] Performance optimizations applied
- [ ] All features tested and working
- [ ] User experience is smooth and responsive

## Next Module Preview
Module 9 will focus on comprehensive testing strategies including unit tests, integration tests, and end-to-end testing. You'll learn to build reliable test suites that ensure your application works correctly across different scenarios and user interactions.