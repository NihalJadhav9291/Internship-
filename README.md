# ITask.tsx
```JavaScript
export interface IAddTaskBody {
    ID: number,
    TaskSubjectId: number,
    TaskName: string,
    Tasktime: string,
    TaskTypeId: number,
    IsReminder: boolean,
    TaskSubjectName: string,
    TaskTypeName: string

}

export interface IGetTaskDetailsBody {
    ID: number
}
```

# ApiTasks.ts
```JavaScript
import { IAddTaskBody, IGetTaskDetailsBody } from "src/interfaces/Task/ITask";
import http from '../../requests/SchoolService/schoolServices';

const AddEmployee = (data: IAddTaskBody) => {
    return http.post<string>('AddTasks', data);
};

const GetTaskDetailsApi = (data: IGetTaskDetailsBody) => {
    return http.post<IAddTaskBody>('GetTaskDetails', data);
};

const GetTaskListApi = () => {
    return http.post<IAddTaskBody[]>('GetTasksList');
};

const GetTaskTypeApi = () => {
    return http.post<IAddTaskBody[]>('GetTaskType');
};

const UpdateTaskDetailsApi = (data: IAddTaskBody) => {
    return http.post<string>('UpdateTaskDetails', data);
};

const DeleteTaskDetailsApi = (data: IGetTaskDetailsBody) => {
    return http.post<string>('DeleteTasks', data);
};
const TaskApi = {
    AddEmployee,
    GetTaskDetailsApi,
    GetTaskTypeApi,
    GetTaskListApi,
    UpdateTaskDetailsApi,
    DeleteTaskDetailsApi
}
export default TaskApi;
```
