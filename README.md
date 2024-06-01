# React + Vite

# Stock List Frontend

This is a frontend project for displaying a list of stocks. The project is built using React and fetches data from a backend API.

## Features

- Displays a paginated list of stocks
- Fetches data from a backend API
- Shows stock name, current price, and date
- Navigation between pages to view more stocks

## Prerequisites

- Node.js and npm installed on your machine

## Getting Started

Clone the repository:
   sh
   git clone <your-repo-url>
   cd <your-repo-directory>

Install dependencies:
  npm install

Start the development server:
npm start

Project Structure
App.js: The main component that fetches and displays stock data.
App.css: The stylesheet for the application.
index.js: The entry point of the application.
Code Overview
App Component
import { useState, useEffect } from "react";
import axios from "axios";
import "./App.css";

function formatDateString(dateString) {
  const date = new Date(dateString);
  const day = String(date.getDate()).padStart(2, "0");
  const month = String(date.getMonth() + 1).padStart(2, "0");
  const year = date.getFullYear();

  return `${day}/${month}/${year}`;
}

function App() {
  const [data, setData] = useState([]);
  const [currentPage, setCurrentPage] = useState(1);
  const [hasMoreData, setHasMoreData] = useState(true);
  const stocksPerPage = 5;

  useEffect(() => {
    loadStocks(currentPage - 1);
  }, [currentPage]);

  const loadStocks = async (page) => {
    try {
      const response = await axios.get(
        `http://localhost:8080/api/v1/stock?pageNo=${page}&pageSize=${stocksPerPage}`
      );
      const stocks = response.data;

      console.log({ stocks });
      setData(stocks.content);
      setHasMoreData(!stocks.last);
    } catch (error) {
      console.error("Error fetching stocks:", error);
    }
  };

  const handleNextPage = () => {
    if (hasMoreData) {
      setCurrentPage((prevPage) => prevPage + 1);
    }
  };

  const handlePrevPage = () => {
    if (currentPage > 1) {
      setCurrentPage((prevPage) => Math.max(prevPage - 1, 1));
    }
  };

  return (
    <div>
      {data.length > 0 && (
        <table>
          <caption>Stock list</caption>
          <thead>
            <tr>
              <th scope="col">Name</th>
              <th scope="col">Price</th>
              <th scope="col">Date</th>
            </tr>
          </thead>
          <tbody>
            {data.map((datum, index) => (
              <tr key={datum.name + index}>
                <td scope="row">{datum.name}</td>
                <td>{datum.currentPrice}</td>
                <td>{formatDateString(datum.createDate)}</td>
              </tr>
            ))}
          </tbody>
          <tfoot>
            <tr>
              <td
                onClick={handlePrevPage}
                style={{
                  cursor: "pointer",
                  visibility: currentPage > 1 ? "visible" : "hidden",
                }}
              >
                {"<"}
              </td>
              <td>Page {currentPage}</td>
              <td
                onClick={handleNextPage}
                style={{
                  cursor: "pointer",
                  visibility: hasMoreData ? "visible" : "hidden",
                }}
              >
                {">"}
              </td>
            </tr>
          </tfoot>
        </table>
      )}
    </div>
  );
}

export default App;



