
# Quick Start a ReactJS app

This repo is consist of some quick start docs that we need when we start a new ReactJS project. Besides react there are some necessary libraries that we need while working on React.



## Process need to follow

### 1. Create a reactJs project 

First open your directory in console where you
want to create your project and insert the following
commands to create and run the project.

```bash
    npx create-react-app hr-management
    cd hr-management
    npm start
```
    
### 2. Set folder structures

These folder structures i'm followin to create a app.
Inside `src` folder create these four folder. Inside `pages` folder
will hold the pages.

    > components
    > features
    > pages
        ↓ leave-application
            LeaveApplication.jsx
    > store 


### 3. Intall vscode extension and create first pages

Add this extension `ES7+ React/Redux/React-Native snippets` to get React snippet for vscode.
create new page component `LeaveApplication.jsx` file inside 
`leave-application` page folder. inside this file write 
`rafc` snippet and hit enter to create a `reactArrowFunctionExportComponent`.

### 4. Install MUI for ReactJs

Install these packages for material UI if you want to
use material UI.

```bash
    npm install @mui/material @emotion/react @emotion/styled
```

### 5. Create a InputField, FormButton component using MUI

Create `InputField.jsx` and `FormButton.jsx` component inside `src > components > form-field`
this folder and `LeaveApplication.jsx` and `LeaveApplication.css` inside
`src > components > form-field`

**InputField.jsx** 
Here Material `TextField` is used to create this component
```bash
import React from 'react'
import { TextField } from '@mui/material'

const InputField = (props) => {
    //destructure the props value for later use
    const{style,type,id,label,name,className,multiline,rows,placeholder,onChange,value,...others}=props
  return (
    <div>
        <TextField 
            style={style}
            type={type} 
            label={label}  
            variant="outlined"  
            onChange={onChange}
            name={name}
            multiline={multiline}
            rows={rows}
            margin='normal'
            placeholder={placeholder}
            value={value}
            {...others}
        />
    </div>
  )
}

export default InputField
```

Install **Material Icons** following this [tutorial](https://mui.com/material-ui/icons/).

```bash
npm install @mui/icons-material
```
**FormButton.jsx** Here Material `Button` and `icons-material` used to create this component
```bash
import React from 'react'
import Button from '@mui/material/Button';
import SaveAltOutlinedIcon from '@mui/icons-material/SaveAltOutlined';
import BorderColorIcon from '@mui/icons-material/BorderColor';
import DeleteForeverIcon from '@mui/icons-material/DeleteForever';

import CancelIcon from '@mui/icons-material/Cancel';

export function SaveButton(props) {
    return (
      <div>
          <Button  
              variant='contained' 
              color='primary'
              size='large'
              endIcon={<SaveAltOutlinedIcon/>}
              style={props.style}
  
              {...props}
              >{props.children}
          </Button> 
      </div>
    )
  }
  
  export function EditButton(props) {
    return (
      <div>
          <Button  
              variant='contained' 
              size='large'
              color='secondary'
              endIcon={<BorderColorIcon/>}
              style={props.style}
              {...props}
              >{props.children}
          </Button> 
      </div>
    )
  }
  
  export function DeleteButton(props) {
    return (
      <div>
          <Button  
              variant='contained' 
              size='large'
              color='warning'
              endIcon={<DeleteForeverIcon/>}
              style={props.style}
              >{props.children}
          </Button> 
      </div>
    )
  }
  
  export function CancelButton(props) {
    return (
      <div>
          <Button  
              variant='contained' 
              size='large'
              color='error'
              endIcon={<CancelIcon/>}
              style={props.style}
              onClick={props.onClick}
              >{props.children}
          </Button> 
      </div>
    )
  }
  

```

**LeaveApplication.jsx** 

Here in Leave application pages form
```bash
import React,{ useState } from 'react'
import InputField from '../../components/form-field/InputField'
import Paper from '@mui/material/Paper';
import './LeaveApplication.css'
import { SaveButton,CancelButton } from '../../components/form-field/FormButton.jsx';

const LeaveApplication = () => {
    //today's date formated as like material date field
    const dateToday=new Date().toISOString().slice(0, 10)

    const initialValue={
        applicationNo:'',
        employeeName:'',
        leaveReason:'',
        fromDate:dateToday,
        toDate:dateToday,
    }

    //formValues state initialize with initial value
    const [formValues, setFormValues]=useState(initialValue)

    //input fields property list for dynamic property based on value
    const inputs=[
      {
        id:1,
        name:'applicationNo',
        label:'Application No',
        type:'text',
      },
      {
        id:2,
        name:'employeeName',
        label:'Employee Name',
        type:'text'
      },
      {
        id:3,
        name:'fromDate',
        label:'From Date',
        type:'date'
      }
      ,
      {
        id:4,
        name:'toDate',
        label:'To Date',
        type:'date'
      },
      {
        id:5,
        name:'leaveReason',
        label:'Leave Reason',
        type:'text',
        multiline: true,
        rows:3 
      },
    ]

    //onchange set property value of targeted property
    const onChange=(e)=>{
        setFormValues({...formValues,[e.target.name]:e.target.value})
        console.log(formValues)
    }
    //clear fileds on button click
    const clearFields=()=>{
        setFormValues({...formValues,...initialValue})
        
    }
    //handle form submit
    const handleSubmit= async (e)=> {
        e.preventDefault()
        console.log(formValues) 
    }
  return (
    <div>
        <h1>Leave Application</h1>
        <Paper elevation={3} style={{margin:'1rem',padding:'1rem'}}>
            <form onSubmit={handleSubmit}>
                {
                    inputs.map((input)=>(
                        <InputField 
                            key={input.id} 
                            style={{marginLeft:'2rem',width:'18rem'}} 
                            {...input} 
                            value={formValues[input.name]}
                            onChange={onChange}
                        />
                    ))
                }
                <div className='btnSection'>
                    <SaveButton type='submit' style={{marginRight:'2em'}}>Save</SaveButton>
                    <CancelButton onClick={clearFields}>Clear</CancelButton>
                </div>
            </form>
        </Paper>
    </div>
  )
}

export default LeaveApplication


```
**LeaveApplication.css**
```bash
form{
    display: flex;
    flex-wrap: wrap;
}

.btnSection{
    margin-top: auto;
    padding-left: 2rem;
    padding-bottom: 1rem;
    flex-basis: content;
    display: flex;
    flex-direction: row;
}

```

Now add the page component in **App.js**
```bash
    <div className="App">
      <LeaveApplication/>
    </div>
```

### 6. Add routing to the application

First install react-router-dom  following this [docs](https://reactrouter.com/en/v6.3.0/getting-started/overview).
```bash
npm install react-router-dom@6
```

Then add the following code in `App.js`

```bash
import './App.css';
import LeaveApplication from './pages/leave-application/LeaveApplication';
import {
  BrowserRouter,
  Routes,
  Route,
} from "react-router-dom";

function App() {
  return (
    <div className="App">
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<LeaveApplication/>}></Route>
        <Route path="leaveapplication" element={<LeaveApplication/>}></Route>
      </Routes>
    </BrowserRouter>
    </div>
  );
}

export default App;

```
### 6. Use formik to manage and validate form

Formik is third party React form library. It provides basic form programming and validation.
Here is te [docs](https://formik.org/docs/tutorial) and
[youtube link](https://youtu.be/vJtyp1YmOpc) where i got this.

First install `formik` and `Yup` using npm
```bash
npm install formik --save
npm install yup --save
```
Then create a new page component **LeaveEntry.jsx** inside `pages>leave-entry`
and write the following code. 
```bash
npm install formik --save
npm install yup --save
```
In ***LeaveEntry.jsx*** do the follwing changes to add formik and Yup code.
Here on submit we just print the form value on console.
```bash
import React from 'react'
import { CancelButton, SaveButton } from '../../components/form-field/FormButton'
import InputField from '../../components/form-field/InputField'
import './LeaveEntry.css'
import { Paper,Table,TableContainer,TableHead,TableRow,TableCell,TableBody } from '@mui/material'
import { useFormik } from 'formik'
import * as Yup from 'yup';

const initialValue={
  leaveName:'',
  allowedDays:0
}

const LeaveEntry = () => {
  const formik=useFormik({
    initialValues:initialValue,
    onSubmit:(values)=>{
      console.log(values)
    },
    validationSchema: Yup.object({
      leaveName: Yup.string()
            .max(10, 'Leave Name must be 10 charcter or less')
            .required('Leave Name is Required'),
      allowedDays: Yup.number()
            .required('Allowed days is Required')
            .max(10,'Max value should be 10'),
    })
  })
  return (
    <div>
        <div className="row">
            <Paper style={{margin:'1rem',padding:'1rem', width:'30%'}}>
                <h2>Leave Type Entry</h2>
                <form onSubmit={formik.handleSubmit}>
                  <InputField 
                    name='leaveName' 
                    label='Leave Name' 
                    style={{width:'90%'}}
                    onChange={formik.handleChange}
                    // onBlur={formik.handleBlur} 
                    value={formik.values.firstName}
                  /> 
                  {formik.touched.leaveName && formik.errors.leaveName ? ( <div>{formik.errors.leaveName}</div>) : null}
                  
                  <InputField 
                    name='allowedDays' 
                    type='number' 
                    label='Allowed Days' 
                    style={{width:'90%'}}
                    onChange={formik.handleChange}
                    // onBlur={formik.handleBlur} 
                    value={formik.values.allowedDays}
                  /> 
                  {formik.touched.allowedDays && formik.errors.allowedDays ? ( <div>{formik.errors.allowedDays}</div>) : null}

                  <div className="btnRow">
                    <SaveButton type='submit'>Save</SaveButton>
                    <CancelButton >Clear</CancelButton>
                  </div>
                </form>
            </Paper>
            <Paper style={{margin:'1rem', width:'70%'}}>
                <div>Just another day</div>
            </Paper>
        </div>
    </div>
  )
}

export default LeaveEntry
```
