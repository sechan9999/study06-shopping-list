# 🛒 Smart Shopping List

> **Real-time collaborative shopping list with cloud synchronization**

[![React](https://img.shields.io/badge/React-18.x-61DAFB?style=flat-square&logo=react)](https://reactjs.org/)
[![Supabase](https://img.shields.io/badge/Supabase-Latest-3ECF8E?style=flat-square&logo=supabase)](https://supabase.com/)
[![JavaScript](https://img.shields.io/badge/JavaScript-ES6+-F7DF1E?style=flat-square&logo=javascript&logoColor=black)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)

A modern, collaborative shopping list application built with React and Supabase. Share your shopping lists in real-time with family and friends, manage items efficiently, and never forget what to buy again!

---

## ✨ Features

### 🎯 Core Functionality
- ✅ **Real-time Synchronization** - Changes appear instantly across all devices
- 👥 **Multi-user Collaboration** - Share lists with family and friends
- 🔐 **Secure Authentication** - User accounts with Supabase Auth
- 📱 **Responsive Design** - Works seamlessly on desktop, tablet, and mobile
- 💾 **Cloud Storage** - All data safely stored in Supabase PostgreSQL
- 🗂️ **Multiple Lists** - Create and manage separate shopping lists

### 🚀 Advanced Features
- 📊 **Shopping History** - Track what you buy and when
- ✏️ **Quick Edit** - Inline editing for fast updates
- 🗑️ **Bulk Actions** - Mark multiple items as purchased or delete
- 🔔 **Real-time Updates** - See changes as others add/remove items
- 📤 **Export/Import** - Backup and restore your lists
- 🎨 **Custom Categories** - Organize items by category

---

## 🏗️ Tech Stack

### Frontend
- **React 18.x** - Component-based UI framework
- **JavaScript (ES6+)** - Modern JavaScript features
- **HTML5/CSS3** - Semantic markup and styling
- **React Hooks** - State management and side effects

### Backend
- **Supabase** - Backend-as-a-Service platform
  - **PostgreSQL** - Relational database
  - **Row Level Security (RLS)** - Fine-grained access control
  - **Real-time Subscriptions** - Live data updates
  - **Authentication** - Email/password and OAuth providers

### Development Tools
- **npm/yarn** - Package management
- **Git** - Version control
- **ESLint** - Code linting
- **Prettier** - Code formatting

---

## 🚀 Quick Start

### Prerequisites

- **Node.js** (v14 or higher)
- **npm** or **yarn**
- **Supabase Account** (free tier available)

### Installation

1. **Clone the repository**
```bash
git clone https://github.com/sechan9999/study06-shopping-list.git
cd study06-shopping-list
```

2. **Install dependencies**
```bash
npm install
# or
yarn install
```

3. **Set up environment variables**

Create a `.env` file in the root directory:

```env
REACT_APP_SUPABASE_URL=your_supabase_project_url
REACT_APP_SUPABASE_ANON_KEY=your_supabase_anon_key
```

Get these values from your [Supabase Dashboard](https://app.supabase.com):
- Go to **Settings** → **API**
- Copy **Project URL** and **anon public** key

4. **Set up Supabase database**

Run the following SQL in your Supabase SQL Editor:

```sql
-- Create shopping_lists table
CREATE TABLE shopping_lists (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  name TEXT NOT NULL,
  user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Create shopping_items table
CREATE TABLE shopping_items (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  list_id UUID REFERENCES shopping_lists(id) ON DELETE CASCADE,
  name TEXT NOT NULL,
  quantity INTEGER DEFAULT 1,
  category TEXT,
  purchased BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Enable Row Level Security
ALTER TABLE shopping_lists ENABLE ROW LEVEL SECURITY;
ALTER TABLE shopping_items ENABLE ROW LEVEL SECURITY;

-- Create policies for shopping_lists
CREATE POLICY "Users can view their own lists" 
  ON shopping_lists FOR SELECT 
  USING (auth.uid() = user_id);

CREATE POLICY "Users can create their own lists" 
  ON shopping_lists FOR INSERT 
  WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update their own lists" 
  ON shopping_lists FOR UPDATE 
  USING (auth.uid() = user_id);

CREATE POLICY "Users can delete their own lists" 
  ON shopping_lists FOR DELETE 
  USING (auth.uid() = user_id);

-- Create policies for shopping_items
CREATE POLICY "Users can view items in their lists" 
  ON shopping_items FOR SELECT 
  USING (
    list_id IN (
      SELECT id FROM shopping_lists WHERE user_id = auth.uid()
    )
  );

CREATE POLICY "Users can create items in their lists" 
  ON shopping_items FOR INSERT 
  WITH CHECK (
    list_id IN (
      SELECT id FROM shopping_lists WHERE user_id = auth.uid()
    )
  );

CREATE POLICY "Users can update items in their lists" 
  ON shopping_items FOR UPDATE 
  USING (
    list_id IN (
      SELECT id FROM shopping_lists WHERE user_id = auth.uid()
    )
  );

CREATE POLICY "Users can delete items in their lists" 
  ON shopping_items FOR DELETE 
  USING (
    list_id IN (
      SELECT id FROM shopping_lists WHERE user_id = auth.uid()
    )
  );

-- Enable Realtime
ALTER PUBLICATION supabase_realtime ADD TABLE shopping_lists;
ALTER PUBLICATION supabase_realtime ADD TABLE shopping_items;
```

5. **Start the development server**
```bash
npm start
# or
yarn start
```

6. **Open your browser**

Navigate to `http://localhost:3000`

---

## 📱 Usage

### Creating Your First List

1. **Sign Up / Log In**
   - Click "Sign Up" to create an account
   - Or log in with existing credentials

2. **Create a New List**
   - Click the "➕ New List" button
   - Enter a name (e.g., "Weekly Groceries")
   - Click "Create"

3. **Add Items**
   - Type an item name in the input field
   - (Optional) Set quantity and category
   - Press Enter or click "Add"

4. **Manage Items**
   - ✅ Check off items as you purchase them
   - ✏️ Click to edit item details
   - 🗑️ Delete items you no longer need
   - 🔄 Real-time sync across all your devices

### Sharing Lists (Coming Soon)

- Generate a share link
- Invite collaborators by email
- Set permissions (view-only or edit)

---

## 🎨 Screenshots

### Desktop View
```
[Add screenshot: screenshots/desktop-view.png]
```

### Mobile View
```
[Add screenshot: screenshots/mobile-view.png]
```

### Real-time Sync Demo
```
[Add GIF: screenshots/realtime-demo.gif]
```

---

## 🏛️ Project Structure

```
study06-shopping-list/
├── public/
│   ├── index.html
│   └── favicon.ico
├── src/
│   ├── components/
│   │   ├── Auth/
│   │   │   ├── Login.jsx
│   │   │   └── SignUp.jsx
│   │   ├── Lists/
│   │   │   ├── ListManager.jsx
│   │   │   ├── ListItem.jsx
│   │   │   └── CreateList.jsx
│   │   ├── Items/
│   │   │   ├── ItemList.jsx
│   │   │   ├── ItemForm.jsx
│   │   │   └── ItemCard.jsx
│   │   └── Layout/
│   │       ├── Header.jsx
│   │       └── Footer.jsx
│   ├── services/
│   │   ├── supabaseClient.js
│   │   ├── authService.js
│   │   └── listService.js
│   ├── hooks/
│   │   ├── useAuth.js
│   │   ├── useLists.js
│   │   └── useRealtimeSubscription.js
│   ├── utils/
│   │   ├── helpers.js
│   │   └── constants.js
│   ├── styles/
│   │   ├── App.css
│   │   └── components/
│   ├── App.jsx
│   └── index.js
├── .env.example
├── .gitignore
├── package.json
└── README.md
```

---

## 🔐 Security Features

- 🔒 **Row Level Security (RLS)** - Users can only access their own data
- 🔑 **Secure Authentication** - Password hashing with bcrypt
- 🛡️ **HTTPS Only** - All communications encrypted
- 🚫 **SQL Injection Protection** - Parameterized queries
- 👤 **User Isolation** - Complete data separation between users

---

## 🚧 Roadmap

### Version 2.0 (Planned)
- [ ] 👥 List sharing with other users
- [ ] 📊 Shopping analytics and insights
- [ ] 🏪 Store-specific lists
- [ ] 💰 Price tracking and budgets
- [ ] 🔔 Push notifications
- [ ] 📱 Progressive Web App (PWA)
- [ ] 🌙 Dark mode
- [ ] 🌍 Multi-language support

### Future Enhancements
- [ ] 🤖 Smart suggestions based on history
- [ ] 📍 Location-based reminders
- [ ] 🎙️ Voice input for adding items
- [ ] 📸 Barcode scanning
- [ ] 🔗 Recipe integration
- [ ] 📧 Email reminders

---

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

### Development Workflow

1. **Fork the repository**
2. **Create a feature branch**
   ```bash
   git checkout -b feature/amazing-feature
   ```
3. **Commit your changes**
   ```bash
   git commit -m "Add amazing feature"
   ```
4. **Push to the branch**
   ```bash
   git push origin feature/amazing-feature
   ```
5. **Open a Pull Request**

### Code Style

- Follow the existing code style
- Use ESLint and Prettier
- Write meaningful commit messages
- Add comments for complex logic

---

## 🐛 Known Issues

- [ ] Mobile keyboard may cover input field on iOS Safari
- [ ] Offline mode not yet supported
- [ ] Large lists (>1000 items) may experience lag

Report bugs by [opening an issue](https://github.com/sechan9999/study06-shopping-list/issues).

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 👤 Author

**Gyver**
- GitHub: [@sechan9999](https://github.com/sechan9999)
- LinkedIn: [Your Profile](https://linkedin.com/in/yourprofile)

---

## 🙏 Acknowledgments

- [React](https://reactjs.org/) - UI framework
- [Supabase](https://supabase.com/) - Backend platform
- [Vercel](https://vercel.com/) - Deployment platform
- Icons from [Heroicons](https://heroicons.com/)

---

## 📚 Learn More

- [React Documentation](https://reactjs.org/docs/getting-started.html)
- [Supabase Documentation](https://supabase.com/docs)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)

---

<div align="center">

**⭐ If you find this project useful, please give it a star! ⭐**

Made with ❤️ by [sechan9999](https://github.com/sechan9999)

</div>
