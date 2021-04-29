# How to Build Laravel 8 and React App

Hello Everyone, Today we will learn how to intigrate React into laravel. for this we will perform one crud oparations and I will explain you how and what libraries required to intigrate React into laravel.

For further learning, this tutorial have two parts

1. backend
2. frontend

for backend please follow my blog related to laravel vue, I am going to use exact backend into this blog. you can find this blog over here(link)

so let's start our second part

1st step: setup react and innstall basic dependencies

laravel is preset with vuejs so first of all we need to change preset to react by following code

composer require laravel/ui
php artisan ui react
npm install

install required dependencies

npm install react-router-dom

let's change bootstarp.js file
resources\js\bootstrap.js

window.\_ = require("lodash");
try {
window.Popper = require("popper.js").default;
window.$ = window.jQuery = require("jquery");

    require("bootstrap");

} catch (e) {}
// The axios part I might talk about
window.axios = require("axios");
window.axios.defaults.headers.common["X-Requested-With"] = "XMLHttpRequest";
let token = document.head.querySelector('meta[name="csrf-token"]');
if (token) {
window.axios.defaults.headers.common["X-CSRF-TOKEN"] = token.content;
} else {
console.error(
"CSRF token not found: https://laravel.com/docs/csrf#csrf-x-csrf-token"
);
}

change app.js file
resources\js\app.js

require("./bootstrap");
import React, { Component } from "react";
import ReactDOM from "react-dom";
import { BrowserRouter, Link, Route, Switch } from "react-router-dom";
import Todos from "./components/Todos";
import CreateTodos from "./components/CreateTodos";
import EditTodos from "./components/EditTodos";
class Index extends Component {
render() {
return (
<BrowserRouter>

<div className="container">
<nav className="navbar navbar-expand-lg">
<div className="collapse navbar-collapse">
<div className="navbar-nav">
<Link to="/" className="nav-item nav-link">
Todos
</Link>
<Link
                                    to="/create"
                                    className="nav-item nav-link"
                                >
Create Todo
</Link>
</div>
</div>
</nav>

                    <Switch>
                        <Route exact path="/" component={Todos} />
                        <Route path="/create" component={CreateTodos} />
                        <Route path="/edit/:id" component={EditTodos} />
                    </Switch>
                </div>
            </BrowserRouter>
        );
    }

}
ReactDOM.render(<Index />, document.getElementById("index"));

Lets Create Todos List File
resources\js\components\Todos.js

import React, { Component } from "react";
import ReactDOM from "react-dom";
import { BrowserRouter, Link, Route, Switch } from "react-router-dom";
import axios from "axios";

export default class Todos extends Component {
constructor(props) {
super(props);

        this.state = { todos: [] };
    }

    componentDidMount() {
        this.getTodos();
    }
    getTodos() {
        axios.get("http://localhost:8000/api/todos/").then((response) => {
            this.setState(() => {
                return { todos: response.data };
            });
        });
    }
    deletetodo(id) {
        axios
            .delete(`http://localhost:8000/api/todos/${id}`)
            .then((response) => {
                this.getTodos();
            });
    }
    render() {
        return (
            <div>
                <h2 className="text-center">Todos List</h2>

                <table className="table">
                    <thead>
                        <tr>
                            <th>ID</th>
                            <th>Name</th>
                            <th>Category</th>
                            <th>Start Date</th>
                            <th>End Date</th>
                            <th>Actions</th>
                        </tr>
                    </thead>
                    <tbody>
                        {this.state.todos.map((todo, index) => (
                            <tr key={todo.id}>
                                <td>{todo.id}</td>
                                <td>{todo.name}</td>
                                <td>{todo.category}</td>
                                <td>{todo.start_date}</td>
                                <td>{todo.end_date}</td>
                                <td>
                                    <div className="btn-group" role="group">
                                        <Link
                                            to={"edit/" + todo.id}
                                            className="btn btn-success"
                                        >
                                            Edit
                                        </Link>
                                        <button
                                            className="btn btn-danger"
                                            onClick={() =>
                                                this.deletetodo(todo.id)
                                            }
                                        >
                                            Delete
                                        </button>
                                    </div>
                                </td>
                            </tr>
                        ))}
                    </tbody>
                </table>
            </div>
        );
    }

}

let's edit CreateTodos.js file
resources\js\components\CreateTodos.js

import React, { Component } from "react";
import ReactDOM from "react-dom";
import { Redirect } from "react-router-dom";
import axios from "axios";

export default class CreateTodos extends Component {
constructor(props) {
super(props);

        this.state = {
            name: "",
            category: "",
            start_date: "",
            end_date: "",
            redirect: false,
        };
        this.OnChangeHandler = this.OnChangeHandler.bind(this);
        this.addTodo = this.addTodo.bind(this);
    }
    addTodo(event) {
        event.preventDefault();
        axios
            .post("http://localhost:8000/api/todos", this.state)
            .then((response) => this.setState({ redirect: true }))
            .catch((err) => console.log(err))
            .finally(() => console.log("done"));
    }
    OnChangeHandler(event) {
        console.log(event);
        let nam = event.target.name;
        let val = event.target.value;
        this.setState({ [nam]: val });
    }
    render() {
        if (this.state.redirect) {
            return <Redirect to="/" />;
        }
        return (
            <div>
                <h3 className="text-center">Create Todo</h3>
                <div className="row">
                    <div className="col-md-6">
                        <form onSubmit={this.addTodo}>
                            <div className="form-group">
                                <label>Name</label>
                                <input
                                    type="text"
                                    name="name"
                                    value={this.state.name}
                                    className="form-control"
                                    onChange={this.OnChangeHandler}
                                />
                            </div>
                            <div className="form-group">
                                <label>Category</label>
                                <select
                                    className="form-control"
                                    name="category"
                                    value={this.state.category}
                                    onChange={this.OnChangeHandler}
                                >
                                    <option value="">
                                        Please Select Category
                                    </option>
                                    <option value="New">New</option>
                                    <option value="Inporgress">
                                        Inporgress
                                    </option>
                                    <option value="QA">QA</option>
                                    <option value="Completed">Completed</option>
                                </select>
                            </div>
                            <div className="form-group">
                                <label>Name</label>
                                <input
                                    type="date"
                                    value={this.state.start_date}
                                    onChange={this.OnChangeHandler}
                                    className="form-control"
                                    name="start_date"
                                />
                            </div>
                            <div className="form-group">
                                <label>Name</label>
                                <input
                                    type="date"
                                    value={this.state.end_date}
                                    onChange={this.OnChangeHandler}
                                    className="form-control"
                                    name="end_date"
                                />
                            </div>
                            <button type="submit" className="btn btn-primary">
                                Create
                            </button>
                        </form>
                    </div>
                </div>
            </div>
        );
    }

}

let's edit EditTodos.js file
resources\js\components\EditTodos.js

import React, { Component } from "react";
import ReactDOM from "react-dom";
import { Redirect } from "react-router-dom";
import axios from "axios";

export default class CreateTodos extends Component {
constructor(props) {
super(props);

        this.state = {
            name: "",
            category: "",
            start_date: "",
            end_date: "",
            redirect: false,
        };
        this.OnChangeHandler = this.OnChangeHandler.bind(this);
        this.addTodo = this.addTodo.bind(this);
    }
    componentDidMount() {
        this.getTodo(this.props.match.params.id);
    }
    getTodo(id) {
        axios.get(`http://localhost:8000/api/todos/${id}`).then((response) => {
            this.setState(() => {
                return {
                    id: response.data.id,
                    name: response.data.name,
                    category: response.data.category,
                    start_date: response.data.start_date,
                    end_date: response.data.end_date,
                    redirect: false,
                };
            });
        });
    }
    addTodo(event) {
        event.preventDefault();
        axios
            .patch(
                `http://localhost:8000/api/todos/${this.state.id}`,
                this.state
            )
            .then((response) => this.setState({ redirect: true }))
            .catch((err) => console.log(err))
            .finally(() => console.log("done"));
    }
    OnChangeHandler(event) {
        console.log(event);
        let nam = event.target.name;
        let val = event.target.value;
        this.setState({ [nam]: val });
    }
    render() {
        if (this.state.redirect) {
            return <Redirect to="/" />;
        }
        return (
            <div>
                <h3 className="text-center">Edit Todo</h3>
                <div className="row">
                    <div className="col-md-6">
                        <form onSubmit={this.addTodo}>
                            <div className="form-group">
                                <label>Name</label>
                                <input
                                    type="text"
                                    name="name"
                                    value={this.state.name}
                                    className="form-control"
                                    onChange={this.OnChangeHandler}
                                />
                            </div>
                            <div className="form-group">
                                <label>Category</label>
                                <select
                                    className="form-control"
                                    name="category"
                                    value={this.state.category}
                                    onChange={this.OnChangeHandler}
                                >
                                    <option value="">
                                        Please Select Category
                                    </option>
                                    <option value="New">New</option>
                                    <option value="Inporgress">
                                        Inporgress
                                    </option>
                                    <option value="QA">QA</option>
                                    <option value="Completed">Completed</option>
                                </select>
                            </div>
                            <div className="form-group">
                                <label>Name</label>
                                <input
                                    type="date"
                                    value={this.state.start_date}
                                    onChange={this.OnChangeHandler}
                                    className="form-control"
                                    name="start_date"
                                />
                            </div>
                            <div className="form-group">
                                <label>Name</label>
                                <input
                                    type="date"
                                    value={this.state.end_date}
                                    onChange={this.OnChangeHandler}
                                    className="form-control"
                                    name="end_date"
                                />
                            </div>
                            <button type="submit" className="btn btn-primary">
                                Create
                            </button>
                        </form>
                    </div>
                </div>
            </div>
        );
    }

}

yeaahhhh!!! we done it....
congratulation now you know how to intigrate react into laravel project. that is it for this blog.

If you have any query hit me on my social media or you can mail me on learning@dreamseekerinfotech.com
