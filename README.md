import { useState } from 'react';

export default function LibraryApp() {
  const [users, setUsers] = useState([]);
  const [books, setBooks] = useState([]);
  const [records, setRecords] = useState([]);
  const [returns, setReturns] = useState([]);

  const [userName, setUserName] = useState('');
  const [bookTitle, setBookTitle] = useState('');
  const [bookAuthor, setBookAuthor] = useState('');

  const addUser = () => {
    const id = users.length + 1;
    setUsers([...users, { id, name: userName }]);
    setUserName('');
  };

  const addBook = () => {
    const id = books.length + 1;
    setBooks([...books, { id, title: bookTitle, author: bookAuthor, available: true }]);
    setBookTitle('');
    setBookAuthor('');
  };

  const borrowBook = (userId, bookId) => {
    const book = books.find(b => b.id === bookId);
    if (book && book.available) {
      setBooks(books.map(b => (b.id === bookId ? { ...b, available: false } : b)));
      setRecords([...records, { userId, bookId, date: new Date().toLocaleString() }]);
    }
  };

  const returnBook = (bookId, userId) => {
    setBooks(books.map(b => (b.id === bookId ? { ...b, available: true } : b)));
    setReturns([...returns, { userId, bookId, date: new Date().toLocaleString() }]);
  };

  const getBorrowCount = (bookId) => {
    return records.filter(r => r.bookId === bookId).length;
  };

  return (
    <div className="p-4 grid gap-6">
      <div className="border rounded shadow p-4">
        <h2 className="text-xl font-bold">新增使用者</h2>
        <input className="border p-1 mr-2" value={userName} onChange={e => setUserName(e.target.value)} placeholder="使用者名稱" />
        <button className="bg-blue-500 text-white px-2 py-1 rounded" onClick={addUser}>新增</button>
      </div>

      <div className="border rounded shadow p-4">
        <h2 className="text-xl font-bold">新增書籍</h2>
        <input className="border p-1 mr-2" value={bookTitle} onChange={e => setBookTitle(e.target.value)} placeholder="書名" />
        <input className="border p-1 mr-2" value={bookAuthor} onChange={e => setBookAuthor(e.target.value)} placeholder="作者" />
        <button className="bg-green-500 text-white px-2 py-1 rounded" onClick={addBook}>新增</button>
      </div>

      <div className="border rounded shadow p-4">
        <h2 className="text-xl font-bold">借書操作</h2>
        {users.map(user => (
          <div key={user.id} className="border p-2 rounded mb-2">
            <div className="font-semibold">{user.name}</div>
            {books.map(book => (
              <button
                key={book.id}
                className={`px-2 py-1 rounded m-1 ${book.available ? 'bg-purple-500 text-white' : 'bg-gray-300 text-gray-500 cursor-not-allowed'}`}
                onClick={() => borrowBook(user.id, book.id)}
                disabled={!book.available}>
                {book.available ? `借《${book.title}》` : `《${book.title}》已借出`}
              </button>
            ))}
          </div>
        ))}
      </div>

      <div className="border rounded shadow p-4">
        <h2 className="text-xl font-bold">借書紀錄</h2>
        {records.map((r, idx) => {
          const user = users.find(u => u.id === r.userId);
          const book = books.find(b => b.id === r.bookId);
          return (
            <div key={idx} className="flex items-center justify-between">
              <span>{user?.name} 借了《{book?.title}》於 {r.date}</span>
              <button className="ml-2 px-2 py-1 border rounded" onClick={() => returnBook(book.id, user.id)}>
                還書
              </button>
            </div>
          );
        })}
      </div>

      <div className="border rounded shadow p-4">
        <h2 className="text-xl font-bold">還書紀錄</h2>
        {returns.map((r, idx) => {
          const user = users.find(u => u.id === r.userId);
          const book = books.find(b => b.id === r.bookId);
          return (
            <div key={idx}>
              {user?.name} 歸還了《{book?.title}》於 {r.date}
            </div>
          );
        })}
      </div>

      <div className="border rounded shadow p-4">
        <h2 className="text-xl font-bold">書籍借閱次數</h2>
        {books.map(book => (
          <div key={book.id}>
            《{book.title}》 共被借閱 {getBorrowCount(book.id)} 次
          </div>
        ))}
      </div>
    </div>
  );
}
