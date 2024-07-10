

## Expense/Budget Tracker

visit at - (https://budget-tracker-seven-delta.vercel.app/)
## Overview

The Expense/Budget Tracker is a web application built using React that helps users manage their budgets and track expenses. It features a user-friendly interface for creating budgets, adding expenses, and viewing detailed spending breakdowns.

## Features

- **Create and Manage Budgets**: Users can create new budgets with a name and amount and view existing budgets with details on total and remaining amounts.
- **Track Expenses**: Users can add expenses to specific budgets, view all expenses, and delete them if necessary.
- **Dynamic UI Updates**: The application dynamically updates the UI to reflect the current state of budgets and expenses.
- **Notifications**: Success and error notifications are displayed using React Toastify to provide feedback on user actions.
- **Responsive Design**: The layout is designed to be responsive and user-friendly on both desktop and mobile devices.

## Technologies Used

- **React**: For building the user interface and managing the state of the application.
- **React Router**: For handling client-side routing and navigation.
- **React Toastify**: For displaying toast notifications to inform users about the success or failure of their actions.
- **Heroicons**: For providing SVG icons used throughout the application for a visually appealing and user-friendly interface.

## Project Structure

### Components

- **AddBudgetForm**: Form component for creating new budgets.
- **AddExpenseForm**: Form component for adding new expenses to budgets.
- **BudgetItem**: Displays individual budget details and includes options to view more details or delete the budget.
- **ExpenseItem**: Displays individual expense details, including options to delete the expense.

### Pages

- **Dashboard**: Main page displaying an overview of all budgets.
- **BudgetPage**: Detailed view of a specific budget, including all associated expenses.
- **ExpensesPage**: View all expenses across all budgets.

### Helpers

- **Helper Functions**: Includes utility functions for formatting currency, calculating percentages, and retrieving data.

### Routing

- **React Router**: Used for handling navigation between pages. The router configuration is set up in `App.jsx` with routes for the dashboard, budget details, and expenses.

## Installation and Setup

1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/expense-budget-tracker.git
   cd expense-budget-tracker
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Start the development server:
   ```bash
   npm start
   ```

4. Open your browser and navigate to `http://localhost:3000` to view the application.

## Usage

### Creating a Budget

1. Navigate to the Dashboard.
2. Click on "Create Budget".
3. Fill in the budget name and amount.
4. Click "Create Budget".

### Adding an Expense

1. Navigate to the desired budget's details page.
2. Click on "Add New Expense".
3. Fill in the expense name and amount.
4. Select the budget category (if applicable).
5. Click "Add Expense".

### Deleting a Budget or Expense

- To delete a budget, navigate to the budget details page and click "Delete Budget".
- To delete an expense, click the delete icon next to the expense in the expenses table.

## Code Samples

### `budgetPage.jsx`

This component fetches budget and expense data using a loader, displays the budget overview, and includes forms for adding new expenses.

```jsx
import { useLoaderData } from "react-router-dom";
import { toast } from "react-toastify";
import AddExpenseForm from "../components/AddExpenseForm";
import BudgetItem from "../components/BudgetItem";
import Table from "../components/Table";
import { createExpense, deleteItem, getAllMatchingItems } from "../helpers";

export async function budgetLoader({ params }) {
  const budget = await getAllMatchingItems({ category: "budgets", key: "id", value: params.id })[0];
  const expenses = await getAllMatchingItems({ category: "expenses", key: "budgetId", value: params.id });

  if (!budget) throw new Error("The budget you’re trying to find doesn’t exist");
  return { budget, expenses };
}

export async function budgetAction({ request }) {
  const data = await request.formData();
  const { _action, ...values } = Object.fromEntries(data);

  if (_action === "createExpense") {
    try {
      createExpense({ name: values.newExpense, amount: values.newExpenseAmount, budgetId: values.newExpenseBudget });
      return toast.success(`Expense ${values.newExpense} created!`);
    } catch (e) {
      throw new Error("There was a problem creating your expense.");
    }
  }

  if (_action === "deleteExpense") {
    try {
      deleteItem({ key: "expenses", id: values.expenseId });
      return toast.success("Expense deleted!");
    } catch (e) {
      throw new Error("There was a problem deleting your expense.");
    }
  }
}

const BudgetPage = () => {
  const { budget, expenses } = useLoaderData();

  return (
    <div className="grid-lg" style={{ "--accent": budget.color }}>
      <h1 className="h2"><span className="accent">{budget.name}</span> Overview</h1>
      <div className="flex-lg">
        <BudgetItem budget={budget} showDelete={true} />
        <AddExpenseForm budgets={[budget]} />
      </div>
      {expenses && expenses.length > 0 && (
        <div className="grid-md">
          <h2><span className="accent">{budget.name}</span> Expenses</h2>
          <Table expenses={expenses} showBudget={false} />
        </div>
      )}
    </div>
  );
};

export default BudgetPage;
```

### `app.jsx`

Sets up the router with routes for the main dashboard, budget details, and expenses.

```jsx
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import { ToastContainer } from "react-toastify";
import "react-toastify/dist/ReactToastify.css";
import Main, { mainLoader } from "./layouts/Main";
import { logoutAction } from "./actions/logout";
import { deleteBudget } from "./actions/deleteBudget";
import Dashboard, { dashboardAction, dashboardLoader } from "./pages/Dashboard";
import Error from "./pages/Error";
import BudgetPage, { budgetAction, budgetLoader } from "./pages/BudgetPage";
import ExpensesPage, { expensesAction, expensesLoader } from "./pages/ExpensesPage";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Main />,
    loader: mainLoader,
    errorElement: <Error />,
    children: [
      {
        index: true,
        element: <Dashboard />,
        loader: dashboardLoader,
        action: dashboardAction,
        errorElement: <Error />,
      },
      {
        path: "budget/:id",
        element: <BudgetPage />,
        loader: budgetLoader,
        action: budgetAction,
        errorElement: <Error />,
        children: [
          {
            path: "delete",
            action: deleteBudget,
          },
        ],
      },
      {
        path: "expenses",
        element: <ExpensesPage />,
        loader: expensesLoader,
        action: expensesAction,
        errorElement: <Error />,
      },
      {
        path: "logout",
        action: logoutAction,
      },
    ],
  },
]);

function App() {
  return (
    <div className="App">
      <RouterProvider router={router} />
      <ToastContainer />
    </div>
  );
}

export default App;
```


Feel free to customize any part of this README to better fit your project.

