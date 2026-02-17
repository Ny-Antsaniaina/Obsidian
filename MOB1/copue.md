
`import React, { createContext, useContext, useEffect, useState, useMemo } from "react";`

`type Transaction = {`
  `id: number;`
  `title: string;`
  `body: string;`
  `date: string; // Assurez-vous que chaque transaction ait une date`
`};`

`interface ListTransactionContextType {`
  `listTransaction: Transaction[];`
  `filteredTransactions: Transaction[];`
  `markedDates: Record<string, any>;`
  `loading: boolean;`
  `page: number;`
  `hasMore: boolean;`
  `limite: number;`
  `selectedDate: string | null;`
  `fetchData: () => Promise<void>;`
  `setLimite: React.Dispatch<React.SetStateAction<number>>;`
  `setSelectedDate: React.Dispatch<React.SetStateAction<string | null>>;`
`}`

`const ListTransactionContext = createContext<ListTransactionContextType | undefined>(undefined);`

`export const ListTransactionContextProvider = ({ children }: { children: React.ReactNode }) => {`
  `const [listTransaction, setListTransaction] = useState<Transaction[]>([]);`
  `const [loading, setLoading] = useState(false);`
  `const [page, setPage] = useState(1);`
  `const [hasMore, setHasMore] = useState(true);`
  `const [limite, setLimite] = useState(20);`
  `const [selectedDate, setSelectedDate] = useState<string | null>(null);`

  `const fetchData = async () => {`
    `if (loading || !hasMore) return;`

    `setLoading(true);`
    `try {`
      `const response = await fetch(`
        ``https://jsonplaceholder.typicode.com/posts?_page=${page}&_limit=${limite}``
      `);`

      `let data = await response.json();`

      `// Ajouter une date aléatoire pour chaque transaction (pour test calendrier)`
      `data = data.map((item: any) => ({`
        `...item,`
        `date: `2026-02-${Math.floor(Math.random() * 28 + 1)`
          `.toString()`
          `.padStart(2, "0")}`,`
      `}));`

      `setListTransaction((prev) => [...prev, ...data]);`
      `setHasMore(data.length === limite);`
      `setPage((prev) => prev + 1);`
    `} catch (e) {`
      `console.error(e);`
    `} finally {`
      `setLoading(false);`
    `}`
  `};`

  `// Transactions filtrées selon la date sélectionnée`
  `const filteredTransactions = useMemo(() => {`
    `if (!selectedDate) return listTransaction;`
    `return listTransaction.filter((tx) => tx.date === selectedDate);`
  `}, [selectedDate, listTransaction]);`

  `// Dates marquées pour le calendrier`
  `const markedDates = useMemo(() => {`
    `const dates: Record<string, any> = {};`
    `listTransaction.forEach((tx) => {`
      `if (tx.date) {`
        `dates[tx.date] = { marked: true, dotColor: "blue" };`
      `}`
    `});`
    `if (selectedDate) {`
      `dates[selectedDate] = { ...dates[selectedDate], selected: true, selectedColor: "#4CAF50" };`
    `}`
    `return dates;`
  `}, [listTransaction, selectedDate]);`

  `// Fetch initial data`
  `useEffect(() => {`
    `fetchData();`
  `}, []);`

  `return (`
    `<ListTransactionContext.Provider`
      `value={{`
        `listTransaction,`
        `filteredTransactions,`
        `markedDates,`
        `loading,`
        `page,`
        `hasMore,`
        `limite,`
        `fetchData,`
        `setLimite,`
        `selectedDate,`
        `setSelectedDate,`
      `}}`
    `>`
      `{children}`
    `</ListTransactionContext.Provider>`
  `);`
`};`

`export const useListTransactionContext = () => {`
  `const context = useContext(ListTransactionContext);`
  `if (!context) {`
    `throw new Error(`
      `"useListTransactionContext must be used inside ListTransactionContextProvider"`
    `);`
  `}`
  `return context;`
`};`
