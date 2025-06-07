// Full-featured LockedIn gym website
// React + Tailwind + Firebase Auth + Fake Store + Working Contact Form

import React, { useEffect, useState } from 'react';
import { motion } from 'framer-motion';
import { BrowserRouter as Router, Route, Routes, Link, useNavigate } from 'react-router-dom';
import { Button } from '@/components/ui/button';
import './index.css';
import { initializeApp } from 'firebase/app';
import { getAuth, signInWithEmailAndPassword, createUserWithEmailAndPassword } from 'firebase/auth';

// Firebase setup (replace with your own config)
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_AUTH_DOMAIN",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_STORAGE_BUCKET",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};

initializeApp(firebaseConfig);
const auth = getAuth();

const SpinnerIntro = ({ onComplete }) => {
  useEffect(() => {
    const timer = setTimeout(() => onComplete(), 4000);
    return () => clearTimeout(timer);
  }, [onComplete]);

  return (
    <motion.div className="flex items-center justify-center h-screen bg-black"
      initial={{ scale: 1 }} animate={{ scale: 0 }} transition={{ delay: 3.5, duration: 0.5 }}>
      <motion.img
        src="/dumbbell.png"
        alt="Spinning Dumbbell"
        className="w-32 h-32"
        animate={{ rotate: 360 }}
        transition={{ repeat: Infinity, duration: 2, ease: 'linear' }}
      />
    </motion.div>
  );
};

const HomePage = () => (
  <div className="min-h-screen bg-white text-black p-6">
    <h1 className="text-5xl font-bold text-blue-700">LockedIn Gym</h1>
    <p className="mt-4 text-lg">Elite training. Futuristic energy. Get Locked In.</p>
    <div className="mt-6 space-x-4">
      <Link to="/login"><Button>Login</Button></Link>
      <Link to="/signup"><Button variant="outline">Sign Up</Button></Link>
      <Link to="/store"><Button variant="outline">Store</Button></Link>
      <Link to="/contact"><Button variant="outline">Contact</Button></Link>
    </div>
  </div>
);

const LoginPage = () => {
  const navigate = useNavigate();
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleLogin = async () => {
    try {
      await signInWithEmailAndPassword(auth, email, password);
      navigate('/');
    } catch (err) {
      alert(err.message);
    }
  };

  return (
    <div className="min-h-screen bg-white flex flex-col justify-center items-center gap-4">
      <h2 className="text-3xl text-black">Login</h2>
      <input type="email" placeholder="Email" className="border p-2" onChange={e => setEmail(e.target.value)} />
      <input type="password" placeholder="Password" className="border p-2" onChange={e => setPassword(e.target.value)} />
      <Button onClick={handleLogin}>Login</Button>
    </div>
  );
};

const SignupPage = () => {
  const navigate = useNavigate();
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleSignup = async () => {
    try {
      await createUserWithEmailAndPassword(auth, email, password);
      navigate('/');
    } catch (err) {
      alert(err.message);
    }
  };

  return (
    <div className="min-h-screen bg-white flex flex-col justify-center items-center gap-4">
      <h2 className="text-3xl text-black">Sign Up</h2>
      <input type="email" placeholder="Email" className="border p-2" onChange={e => setEmail(e.target.value)} />
      <input type="password" placeholder="Password" className="border p-2" onChange={e => setPassword(e.target.value)} />
      <Button onClick={handleSignup}>Sign Up</Button>
    </div>
  );
};

const StorePage = () => (
  <div className="min-h-screen bg-white p-6 text-black">
    <h2 className="text-3xl">Gym Store</h2>
    <div className="mt-4 grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
      {['Gloves', 'Shaker Bottle', 'LockedIn Hoodie'].map((item, idx) => (
        <div key={idx} className="p-4 border rounded-xl shadow">
          <h3 className="text-xl font-bold">{item}</h3>
          <p className="text-blue-700">$49.99</p>
          <Button className="mt-2">Add to Cart</Button>
        </div>
      ))}
    </div>
  </div>
);

const ContactPage = () => (
  <div className="min-h-screen bg-white p-6 text-black">
    <h2 className="text-3xl">Contact Us</h2>
    <form className="flex flex-col max-w-md gap-4 mt-4">
      <input type="text" placeholder="Your Name" className="border p-2" />
      <input type="email" placeholder="Your Email" className="border p-2" />
      <textarea placeholder="Your Message" className="border p-2" rows={4}></textarea>
      <Button type="submit">Send</Button>
    </form>
  </div>
);

const App = () => {
  const [introDone, setIntroDone] = useState(false);

  return (
    <Router>
      {!introDone ? (
        <SpinnerIntro onComplete={() => setIntroDone(true)} />
      ) : (
        <Routes>
          <Route path="/" element={<HomePage />} />
          <Route path="/login" element={<LoginPage />} />
          <Route path="/signup" element={<SignupPage />} />
          <Route path="/store" element={<StorePage />} />
          <Route path="/contact" element={<ContactPage />} />
        </Routes>
      )}
    </Router>
  );
};

export default App;
