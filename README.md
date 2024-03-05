# Task Form

![Task Form image](https://github.com/NihalJadhav9291/Internship-/blob/Task-Form/taskform.png)
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

# RequestTasks.ts
```JavaScript
import { createSlice } from "@reduxjs/toolkit";
import TaskApi from "src/api/Task/ApiTask";
import { IAddTaskBody, IGetTaskDetailsBody } from "src/interfaces/Task/ITask";
import { AppThunk } from "src/store";

const TaskSlice = createSlice({
    name: 'Task',
    initialState: {
        AddTaskmsg: '',
        TaskDetails: null,
        TaskList: [],
        TaskType: [],
        // TaskSubjects: null,
        updateTaskDetailsmsg: '',
        deleteTaskDetailsmsg: '',
        Loading: true
    },
    reducers: {
        getAddTaskMsg(state, action) {
            state.Loading = false;
            state.AddTaskmsg = action.payload;
        },
        resetAddTaskDetails(state) {
            state.Loading = false;
            state.AddTaskmsg = "";
        },
        getTaskDetails(state, action) {
            state.Loading = false;
            state.TaskDetails = action.payload
        },
        getTaskType(state, action) {
            state.Loading = false;
            state.TaskType = action.payload
        },
        getTaskList(state, action) {
            state.Loading = false;
            state.TaskList = action.payload;
        },
        updateTaskDetails(state, action) {
            state.Loading = false;
            state.updateTaskDetailsmsg = action.payload;
        },
        deleteTaskDetails(state, action) {
            state.Loading = false;
            state.deleteTaskDetailsmsg = action.payload
        },
        resetDeleteTaskDetails(state) {
            state.Loading = false;
            state.deleteTaskDetailsmsg = "";
        },
        // getTaskSubjects(state, action) {
        //     state.Loading = false;
        //     state.TaskSubjects = action.payload;
        // },

        getLoading(state, action) {
            state.Loading = true;
        }
    }
});

export const AddTaskDetails =
    (data: IAddTaskBody): AppThunk =>
        async (dispatch) => {
            dispatch(TaskSlice.actions.getLoading(true));
            const response = await TaskApi.AddEmployee(data);
            dispatch(TaskSlice.actions.getAddTaskMsg(response.data));
        };

export const resetAddTaskDetails =
    (): AppThunk =>
        async (dispatch) => {
            dispatch(TaskSlice.actions.resetAddTaskDetails());
        };

export const getTaskDetails =
    (data: IGetTaskDetailsBody): AppThunk =>
        async (dispatch) => {
            dispatch(TaskSlice.actions.getLoading(true));
            const response = await TaskApi.GetTaskDetailsApi(data);

            dispatch(TaskSlice.actions.getTaskDetails(response.data));
        };

export const getTaskList =
    (): AppThunk =>
        async (dispatch) => {
            dispatch(TaskSlice.actions.getLoading(true));
            const response = await TaskApi.GetTaskListApi();
            const responseData = response.data.map((Item, i) => {
                return {
                    Id: Item.ID,
                    Text1: Item.TaskSubjectName,
                    Text2: Item.TaskName,
                    Text3: Item.Tasktime,
                    Text4: Item.TaskTypeName

                };
            })
            dispatch(TaskSlice.actions.getTaskList(responseData));
        };

export const getTaskType =
    (): AppThunk =>
        async (dispatch) => {
            dispatch(TaskSlice.actions.getLoading(true));
            const response = await TaskApi.GetTaskTypeApi();
            const responseData = response.data.map((Item, i) => {
                return {
                    Id: Item.TaskTypeId,
                    Name: Item.TaskTypeName,
                    Value: Item.TaskTypeId.toString()
                };
            });
            dispatch(TaskSlice.actions.getTaskType(responseData));

        };

export const updateTaskDetails =
    (data: IAddTaskBody): AppThunk =>
        async (dispatch) => {
            dispatch(TaskSlice.actions.getLoading(true));
            const response = await TaskApi.UpdateTaskDetailsApi(data);
            dispatch(TaskSlice.actions.updateTaskDetails(response.data))
        }

export const deleteTaskDetails =
    (data: IGetTaskDetailsBody): AppThunk =>
        async (dispatch) => {
            dispatch(TaskSlice.actions.getLoading(true));
            const response = await TaskApi.DeleteTaskDetailsApi(data);
            dispatch(TaskSlice.actions.deleteTaskDetails(response.data));
        };

// export const getTaskSubjects =
//     (data: IGetTaskDetailsBody): AppThunk =>
//         async (dispatch) => {
//             dispatch(TaskSlice.actions.getLoading(true));
//             const response = await TaskApi.getTaskSubjectsApi(data);
//             dispatch(TaskSlice.actions.getTaskSubjects(response.data));
//         };

export const resetDeleteTaskDetails =
    (): AppThunk =>
        async (dispatch) => {
            dispatch(TaskSlice.actions.resetDeleteTaskDetails());
        };
export default TaskSlice.reducer;
```

# TaskForm.ts
```JavaScript
import { Checkbox, Container, FormControlLabel, FormGroup, Grid } from "@mui/material";
import PageHeader from "src/libraries/heading/PageHeader";
import ButtonField from "src/libraries/training/ButtonField";
import Dropdown from "src/libraries/training/Dropdown";
import InputField from "src/libraries/training/InputField";
import RadioList from "src/libraries/training/RadioList";


import { useEffect, useState } from 'react';
import { useDispatch, useSelector } from "react-redux";

import { useNavigate, useParams } from "react-router-dom";
import { toast } from "react-toastify";
import { IAddTaskBody, IGetTaskDetailsBody } from "src/interfaces/Task/ITask";
import CalendarField from "src/libraries/training/CalendarField";
import { AddTaskDetails, getTaskDetails, getTaskType, resetAddTaskDetails } from "src/requests/Task/RequestTask";
import { RootState } from "src/store";
import { getCalendarFormat } from 'src/utils/hooks/Util';

const TaskForm = () => {
    const navigate = useNavigate();
    const dispatch = useDispatch();
    const { Id } = useParams();
    console.log(Id, "Id")


    const [TaskSubject, setTaskSubject] = useState([
        { Id: 1, Name: 'SQL', Value: "1" },
        { Id: 2, Name: 'ASP.Net', Value: "2" },
        { Id: 3, Name: 'React', Value: "3" }
    ])
    const [TaskSubjectId, setTaskSubjectId] = useState('')
    const [TaskName, setTaskName] = useState('')
    const [DateTime, setDateTime] = useState('')
    // const [TaskType, setTaskType] = useState([
    //     { Id: 1, Name: 'Learning', Value: "1" },
    //     { Id: 2, Name: 'Assignment', Value: "2" },
    //     { Id: 3, Name: 'Discussion', Value: "3" }
    // ])
    const [TaskTypeId, setTaskTypeId] = useState(0)
    const [TaskTypeName, setTaskTypeName] = useState('')
    const [isChecked, setIsChecked] = useState(false)

    const AddTaskmsg = useSelector((state: RootState) => state.Task.AddTaskmsg);
    const TaskDetails = useSelector((state: RootState) => state.Task.TaskDetails);
    const TaskType = useSelector((state: RootState) => state.Task.TaskType);
    // const GetTaskSubject = useSelector((state: RootState) => state.Task.TaskSubjects)

    console.log(TaskType);
    const clickTaskSubject = (value) => {
        setTaskSubjectId(value)
    }

    const clickTaskName = (value) => {
        setTaskName(value)
    }

    const clickDateTime = (value) => {
        setDateTime(value)
    }

    const clickTaskType = (value) => {
        setTaskTypeId(value)
    }

    // const clickSubmit = () => {
    //     alert('Task assigned successfully')
    // }

    const clickCancel = () => {
        alert('Task cancelled')
    }

    const handleCheckBoxChange = () => {
        setIsChecked(true)
    }

    useEffect(() => {
        if (AddTaskmsg !== '') {
            toast.success(AddTaskmsg)
            dispatch(resetAddTaskDetails())
            navigate("../../TaskList")

        }
    }, [AddTaskmsg])

    useEffect(() => {
        dispatch(getTaskType())

        if (Id != undefined) {
            const GetTaskDEtailsBody: IGetTaskDetailsBody = {
                ID: Number(Id)
            }
            dispatch(getTaskDetails(GetTaskDEtailsBody))
        }
    }, [])

    useEffect(() => {
        if (TaskDetails !== null) {
            setTaskSubjectId(TaskDetails.TaskSubjectId)
            setTaskName(TaskDetails.TaskName)
            setDateTime(getCalendarFormat(TaskDetails.Tasktime))
            setTaskTypeId(TaskDetails.TaskTypeId)
            setTaskTypeName(TaskDetails.TaskTypeName)
            setIsChecked(TaskDetails.isChecked)
        }
    }, [TaskDetails])

    // useEffect(() => {
    //     if (GetTaskSubject != null) {
    //         setTaskSubjects(GetTaskSubject.TaskSubjects)
    //         setTaskName(GetTaskSubject.TaskName)
    //         setDateTime(GetTaskSubject.DateTime)
    //         setTaskTypeId(GetTaskSubject.TaskType)
    //         setIsChecked(GetTaskSubject.isChecked)
    //     }
    // }, [GetTaskSubject])


    const clickSubmit = () => {
        const AddTaskBody: IAddTaskBody = {

            ID: Number(Id),
            TaskSubjectId: Number(TaskSubjectId),
            TaskName: TaskName,
            Tasktime: DateTime,
            TaskTypeId: TaskTypeId,
            IsReminder: Boolean(isChecked),
            TaskSubjectName: String(TaskSubject),
            TaskTypeName: TaskTypeName


        }
        dispatch(AddTaskDetails(AddTaskBody))

    }


    return (
        <Container>
            <Grid container direction="column" alignItems="center" justifyContent="center" >
                <Grid container spacing={2} >
                    <Grid item xs={12}>
                        <PageHeader heading={'Task Form'} subheading={''} />
                    </Grid>
                    <Grid item xs={12}>
                        <RadioList ItemList={TaskSubject} Label={'Task Subject'}
                            DefaultValue={TaskSubjectId}
                            ClickItem={clickTaskSubject} />
                    </Grid>
                    <Grid item xs={12}>
                        <InputField Item={TaskName} Label={'TaskName'}
                            ClickItem={clickTaskName} />

                    </Grid>

                    <Grid item xs={12}>
                        <CalendarField Item={DateTime} Label={'DateTime'}
                            ClickItem={clickDateTime} />


                    </Grid>

                    <Grid item xs={12}>
                        <Dropdown ItemList={TaskType} Label={'Task type'}
                            DefaultValue={TaskTypeId}
                            ClickItem={clickTaskType} />

                    </Grid>

                    {/* <input type="checkbox" name="Set Reminder" checked={isChecked} onChange={handleCheckBoxChange} /> */}

                    {/* <Checkbox {'SetReminder'} defaultChecked /> */}
                    <FormGroup>
                        <FormControlLabel control={<Checkbox />} label="Set Reminder" />

                    </FormGroup>
                    <Grid item xs={12}>
                        <ButtonField Label={'Submit'}
                            ClickItem={clickSubmit} />

                    </Grid>

                    <Grid item xs={12}>
                        <ButtonField Label={'Cancel'}
                            ClickItem={clickCancel} />

                    </Grid>

                    {/* <input type="checkbox" checked={isChecked} onChange={handleCheckBoxChange} /> */}



                </Grid>


            </Grid>


        </Container>
    )
}

export default TaskForm

```
















