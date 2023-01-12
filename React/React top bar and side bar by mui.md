# React top bar and side bar by mui

Here is the following mui component those we need to develop our top bar and side bar.

---

***For top nav***

- Appbar
- Toolbar
- Tabs

***For side nav***

- Drawer
- List

***Header.jsx***

```jsx
import React from 'react'
import { AppBar,Toolbar,Typography } from '@mui/material';
import Diversity3Icon from '@mui/icons-material/Diversity3';
import Tabs from '@mui/material/Tabs';
import Tab from '@mui/material/Tab';
import { useState } from 'react';
import Sidebar from './Sidebar';
const Header = () => {
    const [value, setvalue] = useState(0)
  return (
    <>
        <AppBar sx={{background:'#425F57'}}>
            <Toolbar>
                <Diversity3Icon></Diversity3Icon>
                <Sidebar/>
                <Tabs sx={{marginLeft:'auto'}} textColor="inherit"  value={value} onChange={(e,value)=>setvalue(value)} indicatorColor='secondary'>
                    <Tab label="Home"></Tab>
                    <Tab label="Setup"></Tab>
                    <Tab label="Leave"></Tab>
                    <Tab label="Shift"></Tab>
                    <Tab label="Payroll"></Tab>
                    <Tab label="Reports"></Tab>
                </Tabs>
            </Toolbar>
        </AppBar>
    </>
  )
}

export default Header

```

---

***Sidebar.jsx***

```jsx
import React,{ useState } from 'react'
import Drawer from '@mui/material/Drawer';
import List from '@mui/material/List';
import { IconButton, ListItemButton, ListItemIcon, ListItemText,ListItem } from '@mui/material';
import InboxIcon from '@mui/icons-material/Inbox';
import MailIcon from '@mui/icons-material/Mail';
import MenuOpenIcon from '@mui/icons-material/MenuOpen';
import Divider from '@mui/material/Divider';

const Sidebar = () => {
    const [openDrawer, setOpenDrawer] = useState(false)
  return (
    <>
        <Drawer open={openDrawer}  variant="persistent" sx= {{ backgroundColor: "pink", color: "red" }}>
        <IconButton onClick={()=>setOpenDrawer(!openDrawer)} style={{marginLeft:'auto'}}>
            <MenuOpenIcon/>
        </IconButton>
        <Divider />

            <List>
                <ListItemText primary="Leave Manage" />
                <Divider />
                {['Inbox', 'Starred', 'Send email', 'Drafts'].map((text, index) => (
                <ListItem key={text} disablePadding>
                    <ListItemButton>
                    <ListItemIcon>
                        {index % 2 === 0 ? <InboxIcon /> : <MailIcon />}
                    </ListItemIcon>
                    <ListItemText primary={text} />
                    </ListItemButton>
                </ListItem>
                ))}
            </List>
            <Divider />
            <List>
                <ListItemText primary="Shift" />
                <Divider />
                {['All mail', 'Trash', 'Spam'].map((text, index) => (
                <ListItem key={text} disablePadding>
                    <ListItemButton>
                    <ListItemIcon>
                        {index % 2 === 0 ? <InboxIcon /> : <MailIcon />}
                    </ListItemIcon>
                    <ListItemText primary={text} />
                    </ListItemButton>
                </ListItem>
            ))}
        </List>
        <Divider />
        </Drawer>
        <IconButton onClick={()=>setOpenDrawer(!openDrawer)}>
            <MenuOpenIcon/>
        </IconButton>
    </>
  )
}

export default Sidebar

```

---