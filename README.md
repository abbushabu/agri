import React, { useState, useEffect, useCallback } from 'react';
import { motion, AnimatePresence } from 'framer-motion';
import {
    MapPin,
    Smartphone,
    Users,
    Search,
    Sliders,
    BookOpen,
    CheckCircle,
    Loader2,
    AlertTriangle,
    XCircle,
    Mail,
    Phone,
    HelpCircle,
    PercentCircle,
    FlaskConical,
    Plant,
    HandMetal,
    LogIn,
    Lock
} from 'lucide-react';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Textarea } from '@/components/ui/textarea';
import {
    Select,
    SelectContent,
    SelectItem,
    SelectTrigger,
    SelectValue,
} from '@/components/ui/select';
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from '@/components/ui/card';
import { Carousel, CarouselContent, CarouselItem, CarouselNext, CarouselPrevious } from "@/components/ui/carousel"
import { cn } from '@/lib/utils';

// ===============================
// Mock Data and Constants
// ===============================

const languages = [
    { code: 'en', name: 'English' },
    { code: 'ta', name: 'Tamil' },
    { code: 'hi', name: 'Hindi' },
    { code: 'ml', name: 'Malayalam' },
];

const resourceTypes = [
    { value: 'human_expert', label: 'Human Expert', icon: <Users className="w-4 h-4 mr-2" /> },
    { value: 'fertilizer', label: 'Fertilizer', icon: <FlaskConical className="w-4 h-4 mr-2" /> },
    { value: 'soil_testing', label: 'Soil Testing', icon: <Plant className="w-4 h-4 mr-2" /> },
    { value: 'financial_aid', label: 'Financial Aid', icon: <PercentCircle className="w-4 h-4 mr-2" /> },
    { value: 'machinery', label: 'Machinery', icon: <HandMetal className="w-4 h-4 mr-2" /> },
];

// Placeholder for farming/plant images (replace with actual URLs)
const farmingImages = [
    'https://placehold.co/600x400/8FBC8F/fff?text=Green+Field&font=Montserrat',
    'https://placehold.co/600x400/228B22/fff?text=Rice+Terrace&font=Montserrat',
    'https://placehold.co/600x400/556B2F/fff?text=Farm+Harvest&font=Montserrat',
    'https://placehold.co/600x400/6B8E23/fff?text=Organic+Farm&font=Montserrat',
    'https://placehold.co/600x400/006400/fff?text=Tea+Plantation&font=Montserrat'
];

const sampleArticles = [
    {
        id: '1',
        title: 'Best Practices for Rice Cultivation',
        content: 'Learn about the latest techniques for maximizing rice yield in Tamil Nadu.',
        language: 'en',
        imageUrl: 'https://placehold.co/600x400/000/fff?text=Rice+Farming&font=Montserrat',
    },
    {
        id: '2',
        title: 'Soil Management in Salem',
        content: 'A comprehensive guide to understanding and improving soil health in the Salem region.',
        language: 'en',
        imageUrl: 'https://placehold.co/600x400/8B4513/fff?text=Soil+Health&font=Montserrat',
    },
    {
        id: '3',
        title: 'பயிர் சுழற்சி முறைகள்',
        content: 'தமிழ்நாட்டில் பயிர் சுழற்சி முறைகள் பற்றி தெரிந்து கொள்ளுங்கள்.',
        language: 'ta',
        imageUrl: 'https://placehold.co/600x400/228B22/fff?text=Crop+Rotation&font=Montserrat',
    },
    {
        id: '4',
        title: 'जैविक खेती के लाभ',
        content: 'जैविक खेती के लाभ और तकनीकें सीखें।',
        language: 'hi',
        imageUrl: 'https://placehold.co/600x400/008000/fff?text=Organic+Farming&font=Montserrat',
    },
    {
        id: '5',
        title: 'നെൽകൃഷിയിലെ മികച്ച രീതികൾ',
        content: 'നെൽകൃഷിയിലെ മികച്ച രീതികളെക്കുറിച്ച് പഠിക്കുക.',
        language: 'ml',
        imageUrl: 'https://placehold.co/600x400/2E8B57/fff?text=Paddy+Farming&font=Montserrat',
    },
    {
        id: '6',
        title: 'Water Conservation Techniques',
        content: "Explore efficient irrigation methods for water conservation.",
        language: 'en',
        imageUrl: 'https://placehold.co/600x400/00BFFF/fff?text=Water+Saving&font=Montserrat'
    },
    {
        id: '7',
        title: 'மாடித்தோட்டம் அமைப்பது எப்படி',
        content: 'வீட்டிலேயே மாடித்தோட்டம் அமைப்பது குறித்த தகவல்கள்.',
        language: 'ta',
        imageUrl: 'https://placehold.co/600x400/8FBC8F/fff?text=Terrace+Garden&font=Montserrat'
    },
    {
        id: '8',
        title: 'मृदा स्वास्थ्य प्रबंधन',
        content: 'मृदा स्वास्थ्य को बनाए रखने और सुधारने के उपाय।',
        language: 'hi',
        imageUrl: 'https://placehold.co/600x400/A0522D/fff?text=Soil+Management&font=Montserrat'
    }
];

// ===============================
// Animation Variants
// ===============================

const cardVariants = {
    hidden: { opacity: 0, y: 20 },
    visible: { opacity: 1, y: 0, transition: { duration: 0.5, ease: 'easeInOut' } },
    exit: { opacity: 0, y: -20, transition: { duration: 0.3 } },
};

const containerVariants = {
    hidden: { opacity: 0 },
    visible: {
        opacity: 1,
        transition: {
            delayChildren: 0.3,
            staggerChildren: 0.2,
        },
    },
};

const formVariants = {
    hidden: { y: 20, opacity: 0 },
    visible: {
        y: 0,
        opacity: 1,
    },
};

const imageVariants = {
    hidden: { opacity: 0, scale: 0.9 },
    visible: { opacity: 1, scale: 1, transition: { duration: 0.8, ease: 'easeInOut' } },
};

// ===============================
// Components
// ===============================

const ArticleCard = ({ article }: { article: typeof sampleArticles[0] }) => (
    <motion.div
        variants={cardVariants}
        initial="hidden"
        animate="visible"
        exit="exit"
        className="w-full"
    >
        <Card className="overflow-hidden border-gray-700 shadow-lg hover:shadow-xl transition-shadow duration-300">
            <div className="relative">
                <img
                    src={article.imageUrl}
                    alt={article.title}
                    className="w-full h-48 object-cover"
                />
            </div>
            <CardHeader>
                <CardTitle className="text-lg text-white">{article.title}</CardTitle>
            </CardHeader>
            <CardContent>
                <p className="text-gray-300 line-clamp-3">{article.content}</p>
            </CardContent>
        </Card>
    </motion.div>
);

const LoginPage = ({ onLogin }: { onLogin: (user: any) => void }) => {
    const [email, setEmail] = useState('');
    const [password, setPassword] = useState('');
    const [loading, setLoading] = useState(false);
    const [message, setMessage] = useState<{ text: string; type: 'success' | 'error' | 'info' } | null>(null);
    const [currentImage, setCurrentImage] = useState(0);

    // Function to handle login (replace with actual authentication)
    const handleLogin = async (e: React.FormEvent) => {
        e.preventDefault();
        setLoading(true);
        setMessage(null); // Clear previous messages

        // Simulate an API call
        try {
            // Replace this with your actual authentication logic
            // const response = await fetch('/api/login', {  //  <---  Backend
            //     method: 'POST',
            //     headers: {
            //         'Content-Type': 'application/json',
            //     },
            //     body: JSON.stringify({ email, password }),
            // });

            // if (response.ok) {
            //     const userData = await response.json();
            //     setMessage({ text: 'Login successful!', type: 'success' });
            //     onLogin(userData); // Pass user data to parent component
            // } else {
            //     const errorData = await response.json();
            //     setMessage({ text: errorData.message || 'Invalid credentials', type: 'error' });
            // }
            await new Promise((resolve) => setTimeout(resolve, 2000));
            if (email && password) { // Simulate successful login
                setMessage({ text: 'Login successful!', type: 'success' });
                const userData = { email: email, name: "Test User" }; //mock user
                onLogin(userData);
            } else {
                setMessage({ text: 'Invalid credentials. Please check your email and password.', type: 'error' });
            }


        } catch (error) {
            setMessage({ text: 'An error occurred. Please try again.', type: 'error' });
        } finally {
            setLoading(false);
        }
    };

    // Image Carousel Effect
    useEffect(() => {
        const intervalId = setInterval(() => {
            setCurrentImage((prevImage) => (prevImage + 1) % farmingImages.length);
        }, 5000); // Change image every 5 seconds

        return () => clearInterval(intervalId); // Cleanup on unmount
    }, []);

    return (
        <div className="min-h-screen bg-gradient-to-br from-gray-900 via-purple-900 to-black flex flex-col md:flex-row">
            {/* Image Section */}
            <motion.div
                variants={imageVariants}
                initial="hidden"
                animate="visible"
                className="md:w-1/2 flex items-center justify-center relative overflow-hidden"
            >
                <motion.img
                    src={farmingImages[currentImage]}
                    alt="Farming Scene"
                    className="absolute inset-0 w-full h-full object-cover transition-opacity duration-1000"
                    style={{
                        zIndex: 1,
                    }}
                    initial={{ opacity: 0 }}
                    animate={{ opacity: 0.7 }} // Add a slight opacity for better text contrast
                />
                <div
                    className="absolute inset-0 bg-gradient-to-t from-black/80 to-transparent"
                    style={{ zIndex: 2 }}
                />
                <div className="relative z-10 text-center space-y-4">
                    <Leaf className="w-16 h-16 mx-auto text-green-400" />
                    <h1 className="text-4xl font-bold text-white">Salem Krishi Seva</h1>
                    <p className="text-gray-300 max-w-md">
                        Welcome to our platform, connecting farmers with the resources they need to thrive.
                    </p>
                </div>
            </motion.div>

            {/* Login Form Section */}
            <motion.div
                variants={containerVariants}
                initial="hidden"
                animate="visible"
                className="md:w-1/2 flex items-center justify-center p-6"
            >
                <motion.div
                    variants={formVariants}
                    className="w-full max-w-md space-y-6"
                >
                    <h2 className="text-3xl font-bold text-white text-center flex items-center justify-center gap-2">
                        <LogIn className="w-6 h-6 text-green-400" />
                        Login
                    </h2>

                    {message && (
                        <div
                            className={cn(
                                'p-3 rounded-md shadow-lg flex items-center gap-2',
                                message.type === 'success' && 'bg-green-500/20 text-green-400 border border-green-500/30',
                                message.type === 'error' && 'bg-red-500/20 text-red-400 border border-red-500/30',
                                message.type === 'info' && 'bg-blue-500/20 text-blue-400 border border-blue-500/30'
                            )}
                        >
                            {message.type === 'success' && <CheckCircle className="w-5 h-5" />}
                            {message.type === 'error' && <AlertTriangle className="w-5 h-5" />}
                            {message.type === 'info' && <LogIn className="w-5 h-5" />}
                            <span>{message.text}</span>
                        </div>
                    )}

                    <form onSubmit={handleLogin} className="space-y-4">
                        <div className="space-y-2">
                            <Label htmlFor="email" className="text-gray-300 flex items-center gap-1">
                                <Mail className="w-4 h-4" />
                                Email
                            </Label>
                            <Input
                                type="email"
                                id="email"
                                value={email}
                                onChange={(e) => setEmail(e.target.value)}
                                placeholder="Enter your email"
                                className="w-full bg-gray-800 border-gray-700 text-white"
                                required
                            />
                        </div>
                        <div className="space-y-2">
                            <Label htmlFor="password" className="text-gray-300 flex items-center gap-1">
                                <Lock className="w-4 h-4" />
                                Password
                            </Label>
                            <Input
                                type="password"
                                id="password"
                                value={password}
                                onChange={(e) => setPassword(e.target.value)}
                                placeholder="Enter your password"
                                className="w-full bg-gray-800 border-gray-700 text-white"
                                required
                            />
                        </div>
                        <Button
                            type="submit"
                            className="w-full bg-green-500 hover:bg-green-600 text-white py-3 rounded-full transition-colors duration-300"
                            disabled={loading}
                        >
                            {loading ? (
                                <>
                                    <Loader2 className="mr-2 h-4 w-4 animate-spin" />
                                    Logging in...
                                </>
                            ) : (
                                'Login'
                            )}
                        </Button>
                    </form>
                    <div className='text-center'>
                        <a href="#" className="text-sm text-gray-400 hover:text-white transition-colors">
                            Forgot Password?
                        </a>
                    </div>
                </motion.div>
            </motion.div>
        </div>
    );
};

const SalemKrishiSeva = () => {
    const [language, setLanguage] = useState('en'); // Default language
    const [resourceType, setResourceType] = useState('');
    const [description, setDescription] = useState('');
    const [searchTerm, setSearchTerm] = useState('');
    const [loading, setLoading] = useState(false);
    const [message, setMessage] = useState<{ text: string; type: 'success' | 'error' | 'info' } | null>(null);
    const [activeSection, setActiveSection] = useState<'home' | 'resources' | 'articles' | 'iot' | 'login'>('home');
    const [user, setUser] = useState<any | null>(null); // State to store logged-in user data

    // Filtered articles based on language and search
    const filteredArticles = sampleArticles.filter(
        (article) =>
            article.language === language &&
            article.title.toLowerCase().includes(searchTerm.toLowerCase())
    );

    // Function to handle resource request submission
    const handleRequestResource = async (e: React.FormEvent) => {
        e.preventDefault();
        if (!resourceType || !description) {
            setMessage({ text: 'Please fill in all fields.', type: 'error' });
            return;
        }

        setLoading(true);
        setMessage(null); // Clear previous messages

        // Simulate an API call
        try {
            // Replace this with your actual API endpoint
            // const response = await fetch('/api/request_resource', {  // <--- Backend
            //     method: 'POST',
            //     headers: {
            //         'Content-Type': 'application/json',
            //     },
            //     body: JSON.stringify({
            //         userId: user.id, //  Use the logged-in user's ID
            //         resourceType,
            //         description,
            //     }),
            // });

            // if (response.ok) {
            //     setMessage({ text: 'Your request has been submitted successfully!', type: 'success' });
            //     setResourceType('');
            //     setDescription('');
            // } else {
            //     const errorData = await response.json();
            //     setMessage({ text: errorData.message || 'Failed to submit request.', type: 'error' });
            // }
            await new Promise((resolve) => setTimeout(resolve, 2000));
            setMessage({
                text: 'Your request has been submitted successfully!',
                type: 'success',
            });
            setResourceType('');
            setDescription('');
        } catch (error) {
            setMessage({
                text: 'An error occurred. Please try again.',
                type: 'error',
            });
        } finally {
            setLoading(false);
        }
    };

    // Function to set message and clear it after a delay
    const showMessage = useCallback((text: string, type: 'success' | 'error' | 'info' = 'info', duration = 5000) => {
        setMessage({ text, type });
        setTimeout(() => {
            setMessage(null);
        }, duration);
    }, []);

    // Function to handle contact form submission
    const handleContactSubmit = async (e: React.FormEvent) => {
        e.preventDefault();
        // Add your contact form submission logic here
        showMessage("Contact form submitted! We'll get back to you soon.", 'success');
    };

    // Mock IoT sensor data (replace with actual data fetching)
    const [sensorData, setSensorData] = useState<{
        temperature: number;
        humidity: number;
        soilMoisture: number;
        lastUpdated: string;
    } | null>(null);

    useEffect(() => {
        const fetchSensorData = () => {
            // Simulate fetching data from sensors
            const newData = {
                temperature: Math.floor(Math.random() * 30) + 20, // 20-50°C
                humidity: Math.floor(Math.random() * 60) + 40, // 40-100%
                soilMoisture: Math.floor(Math.random() * 70) + 30, // 30-100%
                lastUpdated: new Date().toLocaleTimeString(),
            };
            //  Replace with actual fetch
            // fetch('/api/sensor_data')
            //   .then(response => response.json())
            //   .then(data => setSensorData(data))
            //   .catch(error => console.error("Failed to fetch sensor data", error));

            setSensorData(newData);
        };

        fetchSensorData(); // Initial fetch
        const intervalId = setInterval(fetchSensorData, 5000); // Update every 5 seconds

        return () => clearInterval(intervalId); // Cleanup on unmount
    }, []);

    // Simulated Alerts
    const [alerts, setAlerts] = useState<{ type: 'temperature' | 'moisture'; message: string; id: string }[]>([]);

    useEffect(() => {
        if (sensorData) {
            if (sensorData.temperature > 40) {
                setAlerts((prevAlerts) =>
                    prevAlerts.find((alert) => alert.type === 'temperature')
                        ? prevAlerts
                        : [...prevAlerts, { type: 'temperature', message: 'High Temperature Alert!', id: 'temp' }]
                );
            } else {
                setAlerts((prevAlerts) => prevAlerts.filter((alert) => alert.type !== 'temperature'));
            }

            if (sensorData.soilMoisture < 40) {
                setAlerts((prevAlerts) =>
                    prevAlerts.find((alert) => alert.type === 'moisture')
                        ? prevAlerts
                        : [...prevAlerts, { type: 'moisture', message: 'Low Soil Moisture Alert!', id: 'moisture' }]
                );
            } else {
                setAlerts((prevAlerts) => prevAlerts.filter((alert) => alert.type !== 'moisture'));
            }
        }
    }, [sensorData]);

    const removeAlert = (id: string) => {
        setAlerts(prevAlerts => prevAlerts.filter(alert => alert.id !== id));
    };

    // Function to handle login
    const handleLogin = (userData: any) => {
        setUser(userData);
        setActiveSection('home'); // Redirect to home page after login
        showMessage(`Welcome, ${userData.name}!`, 'success');
    };

    // Function to handle logout
    const handleLogout = () => {
        setUser(null); // Clear user data
        setActiveSection('home'); // Redirect to home page after logout
        showMessage('Logged out successfully.', 'info');
    };

    return (
        <div className="min-h-screen bg-gradient-to-br from-gray-900 via-purple-900 to-black text-white">
            {/* Header */}
            <header className="py-4 px-6 md:px-10 lg:px-20 border-b border-gray-800 flex items-center justify-between">
                <div className="flex items-center">
                    <MapPin className="w-6 h-6 text-green-400 mr-2" />
                    <h1 className="text-2xl font-bold">Salem Krishi Seva</h1>
                </div>
                <div className="flex items-center gap-4">
                    <Select value={language} onValueChange={setLanguage}>
                        <SelectTrigger className="w-[180px] bg-gray-800 border-gray-700 text-white">
                            <SelectValue placeholder="Select Language" />
                        </SelectTrigger>
                        <SelectContent className='bg-gray-800 border-gray-700'>
                            {languages.map((lang) => (
                                <SelectItem key={lang.code} value={lang.code} className="hover:bg-gray-700/50 text-white">
                                    {lang.name}
                                </SelectItem>
                            ))}
                        </SelectContent>
                    </Select>
                    <nav className="hidden md:flex gap-6">
                        <Button
                            variant={activeSection === 'home' ? 'default' : 'ghost'}
                            className={cn(
                                "text-white hover:bg-gray-700/50",
                                activeSection === 'home' && "bg-green-500/20 border-green-500/50"
                            )}
                            onClick={() => setActiveSection('home')}
                        >
                            Home
                        </Button>
                        <Button
                            variant={activeSection === 'resources' ? 'default' : 'ghost'}
                            className={cn(
                                "text-white hover:bg-gray-700/50",
                                activeSection === 'resources' && "bg-green-500/20 border-green-500/50"
                            )}
                            onClick={() => setActiveSection('resources')}
                        >
                            Resources
                        </Button>
                        <Button
                            variant={activeSection === 'articles' ? 'default' : 'ghost'}
                            className={cn(
                                "text-white hover:bg-gray-700/50",
                                activeSection === 'articles' && "bg-green-500/20 border-green-500/50"
                            )}
                            onClick={() => setActiveSection('articles')}
                        >
                            Articles
                        </Button>
                        <Button
                            variant={activeSection === 'iot' ? 'default' : 'ghost'}
                            className={cn(
                                "text-white hover:bg-gray-700/50",
                                activeSection === 'iot' && "bg-green-500/20 border-green-500/50"
                            )}
                            onClick={() => setActiveSection('iot')}
                        >
                            IoT Data
                        </Button>
                        {user ? (
                            <Button
                                variant="ghost"
                                className="text-white hover:bg-gray-700/50"
                                onClick={handleLogout}
                            >
                                Logout
                            </Button>
                        ) : (
                            <Button
                                variant={activeSection === 'login' ? 'default' : 'ghost'}
                                className={cn(
                                    "text-white hover:bg-gray-700/50",
                                    activeSection === 'login' && "bg-green-500/20 border-green-500/50"
                                )}
                                onClick={() => setActiveSection('login')}
                            >
                                Login
                            </Button>
                        )}
                    </nav>
                    {/* Mobile Menu (Simplified - use a library for a full implementation) */}
                    <div className="md:hidden">
                        {/* Implement a mobile menu toggle here */}
                        <HelpCircle className="w-6 h-6 text-white cursor-pointer" />
                    </div>
                </div>
            </header>

            {/* Main Content */}
            <main className="p-6 md:p-10 lg:p-20">
                <AnimatePresence>
                    {message && (
                        <motion.div
                            initial={{ opacity: 0, y: -20 }}
                            animate={{ opacity: 1, y: 0 }}
                            exit={{ opacity: 0, y: -20 }}
                            className={cn(
                                'fixed top-4 left-1/2 transform -translate-x-1/2 z-50 p-3 rounded-md shadow-lg',
                                'flex items-center gap-2 min-w-[250px]',
                                message.type === 'success' && 'bg-green-500/20 text-green-400 border border-green-500/30',
                                message.type === 'error' && 'bg-red-500/20 text-red-400 border border-red-500/30',
                                message.type === 'info' && 'bg-blue-500/20 text-blue-400 border border-blue-500/30'
                            )}
                        >
                            {message.type === 'success' && <CheckCircle className="w-5 h-5" />}
                            {message.type === 'error' && <AlertTriangle className="w-5 h-5" />}
                            {message.type === 'info' && <HelpCircle className="w-5 h-5" />}
                            <span>{message.text}</span>
                            <button
                                onClick={() => setMessage(null)}
                                className="ml-auto text-current opacity-70 hover:opacity-100"
                            >
                                <XCircle className="w-4 h-4" />
                            </button>
                        </motion.div>
                    )}
                </AnimatePresence>

                {activeSection === 'home' && (
                    <motion.section
                        initial={{ opacity: 0 }}
                        animate={{ opacity: 1 }}
                        transition={{ duration: 0.8, delay: 0.2 }}
                        className="space-y-8"
                    >
                        <div className="text-center space-y-4">
                            <h2 className="text-4xl font-bold">Welcome to Salem Krishi Seva</h2>
                            <p className="text-gray-300 max-w-2xl mx-auto">
                                Your one-stop platform for accessing personalized agricultural information and resources in Salem, Tamil Nadu.
                            </p>
                            <Button
                                onClick={() => setActiveSection('resources')}
                                className="bg-green-500 hover:bg-green-600 text-white px-8 py-3 rounded-full transition-colors duration-300"
                            >
                                Get Started
                            </Button>
                        </div>

                        <Carousel
                            opts={{
                                align: "center",
                                loop: true,
                            }}
                            className="w-full"
                        >
                            <CarouselContent>
                                {filteredArticles.filter(article => article.language === language).slice(0, 5).map((article) => (
                                    <CarouselItem key={article.id} className="md:basis-1/2 lg:basis-1/3 px-2">
                                        <ArticleCard article={article} />
                                    </CarouselItem>
                                ))}
                            </CarouselContent>
                            <CarouselPrevious className='bg-gray-800/50 text-white border-gray-700' />
                            <CarouselNext className='bg-gray-800/50 text-white border-gray-700' />
                        </Carousel>

                        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
                            <Card className="bg-gray-800 border-gray-700 shadow-lg hover:shadow-xl transition-shadow duration-300">
                                <CardHeader>
                                    <CardTitle className="text-xl text-white flex items-center">
                                        <BookOpen className="w-6 h-6 mr-2 text-green-400" />
                                        Information Hub
                                    </CardTitle>
                                    <CardDescription className="text-gray-300">
                                        Access a wealth of agricultural knowledge.
                                    </CardDescription>
                                </CardHeader>
                                <CardContent>
                                    <p className="text-gray-400">
                                        Find articles, guides, and best practices in your language.
                                    </p>
                                    <Button
                                        variant="outline"
                                        className="mt-4 w-full text-white border-gray-700 hover:bg-gray-700/50"
                                        onClick={() => setActiveSection('articles')}
                                    >
                                        Explore Articles
                                    </Button>
                                </CardContent>
                            </Card>

                            <Card className="bg-gray-800 border-gray-700 shadow-lg hover:shadow-xl transition-shadow duration-300">
                                <CardHeader>
                                    <CardTitle className="text-xl text-white flex items-center">
                                        <Users className="w-6 h-6 mr-2 text-blue-400" />
                                        Resource Requests
                                    </CardTitle>
                                    <CardDescription className="text-gray-300">
                                        Request personalized assistance.
                                    </CardDescription>
                                </CardHeader>
                                <CardContent>
                                    <p className="text-gray-400">
                                        Connect with experts, get soil testing, and more.
                                    </p>
                                    <Button
                                        variant="outline"
                                        className="mt-4 w-full text-white border-gray-700 hover:bg-gray-700/50"
                                        onClick={() => setActiveSection('resources')}
                                    >
                                        Request Resources
                                    </Button>
                                </CardContent>
                            </Card>

                            <Card className="bg-gray-800 border-gray-700 shadow-lg hover:shadow-xl transition-shadow duration-300">
                                <CardHeader>
                                    <CardTitle className="text-xl text-white flex items-center">
                                        <Smartphone className="w-6 h-6 mr-2 text-purple-400" />
                                        IoT Data
                                    </CardTitle>
                                    <CardDescription className="text-gray-300">
                                        Real-time insights from your fields.
                                    </CardDescription>
                                </CardHeader>
                                <CardContent>
                                    <p className="text-gray-400">
                                        Monitor temperature, humidity, and soil moisture.
                                    </p>
                                    <Button
                                        variant="outline"
                                        className="mt-4 w-full text-white border-gray-700 hover:bg-gray-700/50"
                                        onClick={() => setActiveSection('iot')}
                                    >
                                        View IoT Data
                                    </Button>
                                </CardContent>
                            </Card>
                        </div>
                    </motion.section>
                )}

                {activeSection === 'resources' && user && (
                    <motion.section
                        initial={{ opacity: 0, x: 20 }}
                        animate={{ opacity: 1, x: 0 }}
                        transition={{ duration: 0.5 }}
                        className="space-y-6"
                    >
                        <h2 className="text-3xl font-bold">Request Resources</h2>
                        <p className="text-gray-300">
                            Tell us what you need, and we'll connect you with the right resources.
                        </p>
                        <form onSubmit={handleRequestResource} className="space-y-4">
                            <div className="space-y-2">
                                <label htmlFor="resourceType" className="block text-sm font-medium text-gray-200">
                                    Resource Type
                                </label>
                                <Select
                                    value={resourceType}
                                    onValueChange={setResourceType}
                                    disabled={loading}
                                >
                                    <SelectTrigger className="w-full bg-gray-800 border-gray-700 text-white">
                                        <SelectValue placeholder="Select a resource type" />
                                    </SelectTrigger>
                                    <SelectContent className='bg-gray-800 border-gray-700'>
                                        {resourceTypes.map((type) => (
                                            <SelectItem
                                                key={type.value}
                                                value={type.value}
                                                className="hover:bg-gray-700/50 text-white flex items-center"
                                            >
                                                {type.icon} {type.label}
                                            </SelectItem>
                                        ))}
                                    </SelectContent>
                                </Select>
                            </div>
                            <div className="space-y-2">
                                <label htmlFor="description" className="block text-sm font-medium text-gray-200">
                                    Description
                                </label>
                                <Textarea
                                    id="description"
                                    value={description}
                                    onChange={(e) => setDescription(e.target.value)}
                                    placeholder="Describe your request in detail..."
                                    className="w-full bg-gray-800 border-gray-700 text-white min-h-[100px]"
                                    disabled={loading}
                                />
                            </div>
                            <Button
                                type="submit"
                                className="bg-green-500 hover:bg-green-600 text-white px-8 py-3 rounded-full transition-colors duration-300"
                                disabled={loading}
                            >
                                {loading ? (
                                    <>
                                        <Loader2 className="mr-2 h-4 w-4 animate-spin" />
                                        Submitting...
                                    </>
                                ) : (
                                    'Submit Request'
                                )}
                            </Button>
                        </form>
                    </motion.section>
                )}

                {activeSection === 'resources' && !user && (
                    <motion.div
                        initial={{ opacity: 0, x: 20 }}
                        animate={{ opacity: 1, x: 0 }}
                        transition={{ duration: 0.5 }}
                        className="space-y-6"
                    >
                        <h2 className="text-3xl font-bold">Request Resources</h2>
                        <p className="text-gray-300">
                            Please <button
                                onClick={() => setActiveSection('login')}
                                className='text-blue-400 underline hover:text-blue-300'
                            >
                                login
                            </button> to request resources.
                        </p>
                    </motion.div>
                )}

                {activeSection === 'articles' && (
                    <motion.section
                        initial={{ opacity: 0, x: -20 }}
                        animate={{ opacity: 1, x: 0 }}
                        transition={{ duration: 0.5 }}
                        className="space-y-6"
                    >
                        <h2 className="text-3xl font-bold">Agricultural Articles</h2>
                        <p className="text-gray-300">
                            Explore a collection of articles to enhance your farming knowledge.
                        </p>
                        <div className="space-y-4">
                            <Input
                                type="text"
                                placeholder="Search articles..."
                                value={searchTerm}
                                onChange={(e) => setSearchTerm(e.target.value)}
                                className="w-full bg-gray-800 border-gray-700 text-white"
                            />
                        </div>
                        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                            <AnimatePresence>
                                {filteredArticles.map((article) => (
                                    <ArticleCard key={article.id} article={article} />
                                ))}
                            </AnimatePresence>
                        </div>
                        {filteredArticles.length === 0 && (
                            <div className="text-gray-400 text-center py-8">
                                No articles found. Please try a different search term.
                            </div>
                        )}
                    </motion.section>
                )}

                {activeSection === 'iot' && (
                    <motion.section
                        initial={{ opacity: 0, scale: 0.95 }}
                        animate={{ opacity: 1, scale: 1 }}
                        transition={{ duration: 0.5 }}
                        className="space-y-6"
                    >
                        <h2 className="text-3xl font-bold">IoT Sensor Data</h2>
                        <p className="text-gray-300">
                            Monitor real-time data from sensors in your fields.
                        </p>

                        <AnimatePresence>
                            {alerts.map((alert) => (
                                <motion.div
                                    key={alert.id}
                                    initial={{ opacity: 0, y: -10 }}
                                    animate={{ opacity: 1, y: 0 }}
                                    exit={{ opacity: 0, y: -10 }}
                                    className={cn(
                                        'p-4 rounded-md shadow-lg flex items-center justify-between',
                                        'bg-red-500/20 text-red-400 border border-red-500/30'
                                    )}
                                >
                                    <div className='flex items-center gap-2'>
                                        <AlertTriangle className="w-5 h-5" />
                                        <span>{alert.message}</span>
                                    </div>
                                    <button
                                        onClick={() => removeAlert(alert.id)}
                                        className="text-red-400 opacity-70 hover:opacity-100"
                                    >
                                        <XCircle className="w-4 h-4" />
                                    </button>
                                </motion.div>
                            ))}
                        </AnimatePresence>

                        {sensorData ? (
                            <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
                                <Card className="bg-gray-800 border-gray-700 shadow-lg">
                                    <CardHeader>
                                        <CardTitle className="text-xl text-white flex items-center">
                                            Temperature
                                        </CardTitle>
                                    </CardHeader>
                                    <CardContent>
                                        <p className="text-3xl font-semibold text-yellow-400">
                                            {sensorData.temperature}°C
                                        </p>
                                        <p className="text-gray-400">
                                            Last Updated: {sensorData.lastUpdated}
                                        </p>
                                    </CardContent>
                                </Card>

                                <Card className="bg-gray-800 border-gray-700 shadow-lg">
                                    <CardHeader>
                                        <CardTitle className="text-xl text-white flex items-center">
                                            Humidity
                                        </CardTitle>
                                    </CardHeader>
                                    <CardContent>
                                        <p className="text-3xl font-semibold text-blue-400">
                                            {sensorData.humidity}%
                                        </p>
                                        <p className="text-gray-400">
                                            Last Updated: {sensorData.lastUpdated}
                                        </p>
                                    </CardContent>
                                </Card>

                                <Card className="bg-gray-800 border-gray-700 shadow-lg">
                                    <CardHeader>
                                        <CardTitle className="text-xl text-white flex items-center">
                                            Soil Moisture
                                        </CardTitle>
                                    </CardHeader>
                                    <CardContent>
                                        <p className="text-3xl font-semibold text-green-400">
                                            {sensorData.soilMoisture}%
                                        </p>
                                        <p className="text-gray-400">
                                            Last Updated: {sensorData.lastUpdated}
                                        </p>
                                    </CardContent>
                                </Card>
                            </div>
                        ) : (
                            <div className="text-gray-400 text-center py-8">
                                Loading sensor data...
                            </div>
                        )}
                    </motion.section>
                )}

                {activeSection === 'login' && (
                    <LoginPage onLogin={handleLogin} />
                )}
            </main>

            {/* Footer */}
            <footer className="py-6 px-6 md:px-10 lg:px-20 border-t border-gray-800">
                <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                    <div>
                        <h4 className="text-lg font-semibold mb-2">About Us</h4>
                        <p className="text-gray-400">
                            Salem Krishi Seva is dedicated to empowering farmers in Salem, Tamil Nadu, with access to vital agricultural information and resources.
                        </p>
                    </div>
                    <div>
                        <h4 className="text-lg font-semibold mb-2">Contact Us</h4>
                        <ul className="text-gray-400 space-y-1">
                            <li className='flex items-center gap-1'><Mail className='w-4 h-4' /> Email: info@salemkrishi.com</li>
                            <li className='flex items-center gap-1'><Phone className='w-4 h-4' /> Phone: +91 9876543210</li>
                            <li className="flex items-center gap-1"><MapPin className='w-4 h-4' /> Address: 123, Farmer Street, Salem, TN</li>
                        </ul>
                    </div>
                    <div>
                        <h4 className="text-lg font-semibold mb-2">Connect With Us</h4>
                        <div className="flex gap-4">
                            {/* Add social media links here */}
                            <a href="#" className="text-gray-400 hover:text-blue-500">Facebook</a>
                            <a href="#" className="text-gray-400 hover:text-sky-500">Twitter</a>
                            <a href="#" className="text-gray-400 hover:text-red-500">YouTube</a>
                        </div>
                    </div>
                </div>
                <div className="mt-4 text-center text-gray-500">
                    &copy; {new Date().getFullYear()} Salem Krishi Seva. All rights reserved.
                </div>
            </footer>
        </div>
    );
};

export default SalemKrishiSeva;
