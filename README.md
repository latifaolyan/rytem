<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>رِتم - Rhythm</title>
    <script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@300;400;500;700;900&family=Amiri:wght@400;700&display=swap" rel="stylesheet">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { font-family: 'Tajawal', sans-serif; }
        @keyframes twinkle {
            0%, 100% { opacity: 0.3; }
            50% { opacity: 1; }
        }
        @keyframes pulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.05); }
        }
        @keyframes celebrate {
            0%, 100% { transform: translateY(0) rotate(0deg); }
            25% { transform: translateY(-10px) rotate(-5deg); }
            75% { transform: translateY(-10px) rotate(5deg); }
        }
        @keyframes morph {
            0% { transform: scaleX(1) scaleY(1); }
            50% { transform: scaleX(0.95) scaleY(1.05); }
            100% { transform: scaleX(1) scaleY(1); }
        }
    </style>
</head>
<body>
    <div id="root"></div>
    <script type="text/babel">
        const { useState, useRef, useEffect } = React;

        function RhythmApp() {
          const [currentStep, setCurrentStep] = useState('onboarding');
          const [userProfile, setUserProfile] = useState({name: '', healthData: {}, medications: []});
          const [dailyPhoto, setDailyPhoto] = useState(null);
          const [photoAnalysis, setPhotoAnalysis] = useState(null);
          const [previousPhotos, setPreviousPhotos] = useState([]);
          const [bodyMeasurements, setBodyMeasurements] = useState([]);
          const [showMeasurementForm, setShowMeasurementForm] = useState(false);
          const [currentMeasurement, setCurrentMeasurement] = useState({weight: '', waist: '', chest: '', arms: '', hips: ''});
          const [points, setPoints] = useState(0);
          const [streak, setStreak] = useState(0);
          const [habitsDone, setHabitsDone] = useState({});
          const [medicationsTaken, setMedicationsTaken] = useState({});
          const [selectedLocation, setSelectedLocation] = useState('المنزل');
          const [showProfile, setShowProfile] = useState(false);
          const [steps, setSteps] = useState(0);
          const [lastWalkReminder, setLastWalkReminder] = useState(Date.now());
          const [showComparison, setShowComparison] = useState(false);
          const dailyPhotoRef = useRef(null);

          useEffect(() => {
            const walkInterval = setInterval(() => {
              const now = Date.now();
              if (now - lastWalkReminder >= 2 * 60 * 60 * 1000) {
                alert('⏰ حان وقت المشي! قم وامشِ قليلاً 🚶');
                setLastWalkReminder(now);
              }
            }, 60000);
            return () => clearInterval(walkInterval);
          }, [lastWalkReminder]);

          const notifications = [];
          if (userProfile.medications && userProfile.medications.length > 0) {
            notifications.push({ id: 'med', message: 'لا تنسى علاجك اليومي 💊', color: '#D4AF37', time: 'الآن' });
          }
          notifications.push({ id: 1, message: 'حان وقت شرب الماء! 💧', color: '#C9A961', time: 'الآن' });
          if (steps < 10000) {
            notifications.push({ id: 2, message: 'قم وامشِ - هدفك 10,000 خطوة! 🚶', color: '#D4AF37', time: 'الآن' });
          }

          const habits = [
            { id: 1, name: 'اتباع نظام غذائي', points: 30, icon: '🥗', type: 'positive' },
            { id: 2, name: '10,000 خطوة', points: 25, icon: '👟', type: 'positive', progress: steps },
            { id: 3, name: 'عدم التسرع في الأكل', points: 20, icon: '🍽️', type: 'positive' },
            { id: 4, name: 'السهر', points: 25, icon: '🌙', type: 'negative' }
          ];

          const getMembershipLevel = (pts) => {
            if (pts >= 1000) return { level: 'ذهبي', reward: 'اشتراك جيم مجاني', color: '#D4AF37', icon: '🏆' };
            if (pts >= 500) return { level: 'فضي', reward: 'اشتراك أكل صحي', color: '#C0C0C0', icon: '🥈' };
            if (pts >= 200) return { level: 'برونزي', reward: 'خصم على الموقع', color: '#CD7F32', icon: '🥉' };
            return { level: 'عادي', reward: 'لا يوجد', color: '#94A3B8', icon: '⭐' };
          };

          const generateDietPlan = () => {
            const { healthData, medications } = userProfile;
            const hasDiseases = healthData.hasDiseases;
            const bmi = healthData.weight / Math.pow(healthData.height / 100, 2);
            
            let plan = `مرحباً ${userProfile.name}! 🌟\n\n`;
            
            if (hasDiseases) {
              plan += `نظراً لوجود أمراض مزمنة، إليك نظام غذائي مناسب:\n\n`;
              plan += `🥗 الإفطار: شوفان مع الفواكه والمكسرات\n`;
              plan += `🍲 الغداء: صدر دجاج مشوي + أرز بني + سلطة خضراء\n`;
              plan += `🥙 العشاء: سمك مشوي + خضار مطهوة على البخار\n\n`;
              plan += `⚠️ تجنب: السكريات، الملح الزائد، الأطعمة المقلية\n`;
              plan += `💊 لا تنسى أدويتك: ${medications.join(', ')}`;
            } else if (bmi > 25) {
              plan += `نظام غذائي لتخفيف الوزن:\n\n`;
              plan += `🥗 الإفطار: بيض مسلوق + خبز أسمر + خيار\n`;
              plan += `🍲 الغداء: دجاج مشوي + كينوا + سلطة\n`;
              plan += `🥙 العشاء: زبادي يوناني + فواكه\n\n`;
              plan += `💡 نصيحة: اشرب 8 أكواب ماء يومياً`;
            } else {
              plan += `نظام غذائي متوازن:\n\n`;
              plan += `🥗 الإفطار: بيض + خبز + عصير برتقال\n`;
              plan += `🍲 الغداء: لحم + أرز + سلطة + فواكه\n`;
              plan += `🥙 العشاء: دجاج أو سمك + خضار\n\n`;
              plan += `✨ استمر على هذا النظام الصحي!`;
            }
            
            return plan;
          };

          const BodyAvatar = ({ measurement, isOld = false, showMedal = false }) => {
            if (!measurement) return null;
            
            const { weight, waist, chest, arms, hips } = measurement;
            const height = userProfile.healthData.height || 170;
            
            const waistScale = Math.min(Math.max(parseFloat(waist) / 80, 0.7), 1.3);
            const chestScale = Math.min(Math.max(parseFloat(chest) / 95, 0.8), 1.2);
            const armScale = Math.min(Math.max(parseFloat(arms || 30) / 30, 0.7), 1.2);
            const hipScale = Math.min(Math.max(parseFloat(hips || 100) / 100, 0.8), 1.2);
            
            const color = isOld ? '#94A3B8' : '#D4AF37';
            const opacity = isOld ? 0.5 : 1;

            return (
              <div style={{position: 'relative', width: '200px', height: '400px', margin: '0 auto'}}>
                <svg width="200" height="400" viewBox="0 0 200 400" style={{filter: `drop-shadow(0 10px 30px ${color}66)`}}>
                  <circle cx="100" cy="50" r="30" fill={color} opacity={opacity} style={{animation: showMedal ? 'celebrate 2s infinite' : 'none'}} />
                  <ellipse cx="100" cy="140" rx={40 * chestScale} ry="60" fill={color} opacity={opacity} style={{animation: isOld ? 'none' : 'morph 3s infinite'}} />
                  <ellipse cx="100" cy="200" rx={35 * waistScale} ry="30" fill={color} opacity={opacity} style={{animation: isOld ? 'none' : 'morph 3s infinite 0.5s'}} />
                  <ellipse cx="100" cy="250" rx={45 * hipScale} ry="40" fill={color} opacity={opacity} />
                  <ellipse cx="60" cy="140" rx={12 * armScale} ry="70" fill={color} opacity={opacity} transform="rotate(-20 60 140)" />
                  <ellipse cx="140" cy="140" rx={12 * armScale} ry="70" fill={color} opacity={opacity} transform="rotate(20 140 140)" />
                  <ellipse cx="85" cy="330" rx="18" ry="80" fill={color} opacity={opacity} />
                  <ellipse cx="115" cy="330" rx="18" ry="80" fill={color} opacity={opacity} />
                  <circle cx="90" cy="45" r="4" fill="#1a1410" opacity={opacity} />
                  <circle cx="110" cy="45" r="4" fill="#1a1410" opacity={opacity} />
                  <path d="M 85 58 Q 100 65 115 58" stroke="#1a1410" strokeWidth="3" fill="none" opacity={opacity} strokeLinecap="round" />
                </svg>
                
                {showMedal && (
                  <div style={{position: 'absolute', top: '10px', right: '10px', fontSize: '50px', animation: 'pulse 1s infinite'}}>🏆</div>
                )}
                
                <div style={{textAlign: 'center', marginTop: '20px', color: isOld ? '#94A3B8' : '#D4AF37'}}>
                  <p style={{fontSize: '24px', fontWeight: 'bold', fontFamily: 'Amiri, serif'}}>{weight} كجم</p>
                  <p style={{fontSize: '14px', color: '#C9A961'}}>{measurement.date || new Date().toLocaleDateString('ar-SA')}</p>
                </div>
              </div>
            );
          };

          const OnboardingScreen = () => {
            const [step, setStep] = useState(1);
            const [name, setName] = useState('');
            const [healthData, setHealthData] = useState({height: '', weight: '', age: '', hasDiseases: null});
            const [medications, setMedications] = useState(['']);

            const completeOnboarding = () => {
              setUserProfile({ name, healthData, medications: healthData.hasDiseases ? medications.filter(m => m.trim()) : [] });
              setCurrentStep('dashboard');
            };

            const addMedication = () => setMedications([...medications, '']);
            const updateMedication = (index, value) => {
              const newMeds = [...medications];
              newMeds[index] = value;
              setMedications(newMeds);
            };
            const removeMedication = (index) => setMedications(medications.filter((_, i) => i !== index));

            if (step === 1) {
              return (
                <div style={{minHeight: '100vh', background: 'linear-gradient(135deg, #0a0e27 0%, #16213e 50%, #0a0e27 100%)', display: 'flex', alignItems: 'center', justifyContent: 'center', padding: '24px', position: 'relative'}}>
                  <div style={{position: 'absolute', top: 0, left: 0, width: '100%', height: '100%', pointerEvents: 'none'}}>
                    {[...Array(40)].map((_, i) => (
                      <div key={i} style={{position: 'absolute', width: '2px', height: '2px', background: '#D4AF37', borderRadius: '50%', left: Math.random() * 100 + '%', top: Math.random() * 100 + '%', animation: `twinkle ${2 + Math.random() * 3}s infinite`}}></div>
                    ))}
                  </div>
                  <div style={{maxWidth: '480px', width: '100%', background: 'linear-gradient(135deg, #2a2418 0%, #3d3124 100%)', backdropFilter: 'blur(20px)', border: '2px solid #D4AF37', borderRadius: '32px', padding: '48px', boxShadow: '0 25px 50px rgba(0, 0, 0, 0.5), 0 0 60px rgba(212, 175, 55, 0.3)', position: 'relative', zIndex: 10}}>
                    <div style={{textAlign: 'center', marginBottom: '40px'}}>
                      <div style={{background: 'linear-gradient(135deg, #D4AF37 0%, #F4E5C2 100%)', width: '100px', height: '100px', borderRadius: '50%', display: 'flex', alignItems: 'center', justifyContent: 'center', margin: '0 auto 24px', fontSize: '48px', boxShadow: '0 20px 40px rgba(212, 175, 55, 0.5)'}}>✨</div>
                      <h1 style={{fontFamily: 'Amiri, serif', fontSize: '48px', fontWeight: 'bold', background: 'linear-gradient(135deg, #D4AF37 0%, #F4E5C2 100%)', WebkitBackgroundClip: 'text', WebkitTextFillColor: 'transparent', marginBottom: '12px'}}>رِتم</h1>
                      <p style={{color: '#F4E5C2', fontSize: '18px'}}>ابدأ رحلتك الصحية</p>
                    </div>
                    <div>
                      <label style={{display: 'block', color: '#F4E5C2', fontWeight: '600', marginBottom: '12px', fontSize: '16px'}}>اسمك</label>
                      <input type="text" value={name} onChange={(e) => setName(e.target.value)} placeholder="أدخل اسمك" style={{width: '100%', background: 'rgba(15, 15, 10, 0.5)', border: '2px solid rgba(212, 175, 55, 0.4)', borderRadius: '16px', padding: '16px 20px', color: '#F4E5C2', outline: 'none', fontSize: '16px'}} />
                      <button onClick={() => setStep(2)} disabled={!name} style={{width: '100%', background: name ? 'linear-gradient(135deg, #D4AF37 0%, #C9A961 100%)' : 'rgba(80, 80, 80, 0.4)', padding: '18px', borderRadius: '16px', fontWeight: 'bold', color: name ? '#1a1410' : '#888', marginTop: '32px', cursor: name ? 'pointer' : 'not-allowed', border: 'none', fontSize: '18px', boxShadow: name ? '0 10px 30px rgba(212, 175, 55, 0.4)' : 'none'}}>التالي</button>
                    </div>
                  </div>
                </div>
              );
            }

            return (
              <div style={{minHeight: '100vh', background: 'linear-gradient(135deg, #0a0e27 0%, #16213e 50%, #0a0e27 100%)', display: 'flex', alignItems: 'center', justifyContent: 'center', padding: '24px', position: 'relative'}}>
                <div style={{position: 'absolute', top: 0, left: 0, width: '100%', height: '100%', pointerEvents: 'none'}}>
                  {[...Array(40)].map((_, i) => (
                    <div key={i} style={{position: 'absolute', width: '2px', height: '2px', background: '#D4AF37', borderRadius: '50%', left: Math.random() * 100 + '%', top: Math.random() * 100 + '%', animation: `twinkle ${2 + Math.random() * 3}s infinite`}}></div>
                  ))}
                </div>
                <div style={{maxWidth: '480px', width: '100%', background: 'linear-gradient(135deg, #2a2418 0%, #3d3124 100%)', backdropFilter: 'blur(20px)', border: '2px solid #D4AF37', borderRadius: '32px', padding: '40px', maxHeight: '90vh', overflowY: 'auto', boxShadow: '0 25px 50px rgba(0, 0, 0, 0.5), 0 0 60px rgba(212, 175, 55, 0.3)', position: 'relative', zIndex: 10}}>
                  <h2 style={{fontFamily: 'Amiri, serif', fontSize: '32px', fontWeight: 'bold', color: '#D4AF37', marginBottom: '32px', textAlign: 'center'}}>بياناتك الصحية</h2>
                  <div style={{display: 'grid', gridTemplateColumns: '1fr 1fr', gap: '16px', marginBottom: '20px'}}>
                    <div>
                      <label style={{display: 'block', color: '#F4E5C2', fontWeight: '600', fontSize: '14px', marginBottom: '10px'}}>الطول (سم)</label>
                      <input type="number" value={healthData.height} onChange={(e) => setHealthData({...healthData, height: e.target.value})} placeholder="170" style={{width: '100%', background: 'rgba(15, 15, 10, 0.5)', border: '2px solid rgba(212, 175, 55, 0.4)', borderRadius: '12px', padding: '14px', color: '#F4E5C2', outline: 'none'}} />
                    </div>
                    <div>
                      <label style={{display: 'block', color: '#F4E5C2', fontWeight: '600', fontSize: '14px', marginBottom: '10px'}}>الوزن (كجم)</label>
                      <input type="number" value={healthData.weight} onChange={(e) => setHealthData({...healthData, weight: e.target.value})} placeholder="70" style={{width: '100%', background: 'rgba(15, 15, 10, 0.5)', border: '2px solid rgba(212, 175, 55, 0.4)', borderRadius: '12px', padding: '14px', color: '#F4E5C2', outline: 'none'}} />
                    </div>
                  </div>
                  <div style={{marginBottom: '20px'}}>
                    <label style={{display: 'block', color: '#F4E5C2', fontWeight: '600', fontSize: '14px', marginBottom: '10px'}}>العمر</label>
                    <input type="number" value={healthData.age} onChange={(e) => setHealthData({...healthData, age: e.target.value})} placeholder="25" style={{width: '100%', background: 'rgba(15, 15, 10, 0.5)', border: '2px solid rgba(212, 175, 55, 0.4)', borderRadius: '12px', padding: '14px', color: '#F4E5C2', outline: 'none'}} />
                  </div>
                  <div style={{marginBottom: '20px', padding: '20px', background: 'rgba(212, 175, 55, 0.15)', border: '2px solid #D4AF37', borderRadius: '16px'}}>
                    <label style={{display: 'block', color: '#F4E5C2', fontWeight: '600', fontSize: '15px', marginBottom: '16px'}}>هل لديك أمراض مزمنة؟</label>
                    <div style={{display: 'flex', gap: '12px'}}>
                      <button onClick={() => setHealthData({...healthData, hasDiseases: true})} style={{flex: 1, padding: '14px', background: healthData.hasDiseases === true ? 'linear-gradient(135deg, #D4AF37 0%, #C9A961 100%)' : 'rgba(15, 15, 10, 0.5)', border: '2px solid rgba(212, 175, 55, 0.4)', borderRadius: '12px', color: healthData.hasDiseases === true ? '#1a1410' : '#F4E5C2', fontWeight: '700', cursor: 'pointer'}}>نعم</button>
                      <button onClick={() => {setHealthData({...healthData, hasDiseases: false}); setMedications(['']);}} style={{flex: 1, padding: '14px', background: healthData.hasDiseases === false ? 'linear-gradient(135deg, #D4AF37 0%, #C9A961 100%)' : 'rgba(15, 15, 10, 0.5)', border: '2px solid rgba(212, 175, 55, 0.4)', borderRadius: '12px', color: healthData.hasDiseases === false ? '#1a1410' : '#F4E5C2', fontWeight: '700', cursor: 'pointer'}}>لا</button>
                    </div>
                  </div>
                  {healthData.hasDiseases && (
                    <div style={{marginBottom: '20px', padding: '20px', background: 'rgba(212, 175, 55, 0.12)', border: '2px solid rgba(212, 175, 55, 0.4)', borderRadius: '16px'}}>
                      <label style={{display: 'block', color: '#D4AF37', fontWeight: '600', fontSize: '16px', marginBottom: '16px'}}>💊 الأدوية اليومية</label>
                      {medications.map((med, index) => (
                        <div key={index} style={{display: 'flex', gap: '10px', marginBottom: '12px'}}>
                          <input type="text" value={med} onChange={(e) => updateMedication(index, e.target.value)} placeholder={'دواء ' + (index + 1)} style={{flex: 1, background: 'rgba(15, 15, 10, 0.5)', border: '2px solid rgba(212, 175, 55, 0.4)', borderRadius: '12px', padding: '12px 16px', color: '#F4E5C2', fontSize: '15px', outline: 'none'}} />
                          {medications.length > 1 && (
                            <button onClick={() => removeMedication(index)} style={{padding: '12px 18px', background: 'rgba(212, 175, 55, 0.25)', border: '2px solid #D4AF37', borderRadius: '12px', color: '#D4AF37', cursor: 'pointer', fontSize: '18px', fontWeight: 'bold'}}>✕</button>
                          )}
                        </div>
                      ))}
                      <button onClick={addMedication} style={{width: '100%', padding: '12px', background: 'rgba(212, 175, 55, 0.2)', border: '2px solid #D4AF37', borderRadius: '12px', color: '#D4AF37', fontWeight: '700', cursor: 'pointer'}}>+ إضافة دواء</button>
                    </div>
                  )}
                  <button onClick={completeOnboarding} disabled={!healthData.height || !healthData.weight || !healthData.age || healthData.hasDiseases === null} style={{width: '100%', background: (healthData.height && healthData.weight && healthData.age && healthData.hasDiseases !== null) ? 'linear-gradient(135deg, #D4AF37 0%, #C9A961 100%)' : 'rgba(80, 80, 80, 0.4)', padding: '18px', borderRadius: '16px', fontWeight: 'bold', color: (healthData.height && healthData.weight && healthData.age && healthData.hasDiseases !== null) ? '#1a1410' : '#888', cursor: (healthData.height && healthData.weight && healthData.age && healthData.hasDiseases !== null) ? 'pointer' : 'not-allowed', border: 'none', fontSize: '18px', boxShadow: (healthData.height && healthData.weight && healthData.age && healthData.hasDiseases !== null) ? '0 10px 30px rgba(212, 175, 55, 0.4)' : 'none'}}>ابدأ الآن</button>
                </div>
              </div>
            );
          };

          const ProfileModal = () => (
            <div style={{position: 'fixed', top: 0, left: 0, right: 0, bottom: 0, background: 'rgba(0,0,0,0.85)', display: 'flex', alignItems: 'center', justifyContent: 'center', zIndex: 1000, padding: '24px', backdropFilter: 'blur(10px)'}} onClick={() => setShowProfile(false)}>
              <div onClick={(e) => e.stopPropagation()} style={{background: 'linear-gradient(135deg, #2a2418 0%, #3d3124 100%)', border: '2px solid #D4AF37', borderRadius: '32px', padding: '40px', maxWidth: '540px', width: '100%', boxShadow: '0 25px 50px rgba(0, 0, 0, 0.7), 0 0 80px rgba(212, 175, 55, 0.3)'}}>
                <div style={{display: 'flex', justifyContent: 'space-between', marginBottom: '32px'}}>
                  <h2 style={{fontFamily: 'Amiri, serif', fontSize: '32px', fontWeight: 'bold', color: '#D4AF37'}}>📊 إنجازاتك</h2>
                  <button onClick={() => setShowProfile(false)} style={{background: 'none', border: 'none', color: '#C9A961', fontSize: '28px', cursor: 'pointer'}}>✕</button>
                </div>
                <div style={{background: 'rgba(212, 175, 55, 0.2)', border: '2px solid #D4AF37', borderRadius: '20px', padding: '32px', marginBottom: '32px', textAlign: 'center'}}>
                  <div style={{fontSize: '64px', marginBottom: '16px'}}>👤</div>
                  <h3 style={{fontSize: '28px', fontWeight: 'bold', color: '#F4E5C2'}}>{userProfile.name}</h3>
                </div>
                <div style={{display: 'grid', gridTemplateColumns: 'repeat(3, 1fr)', gap: '16px'}}>
                  <div style={{background: 'rgba(212, 175, 55, 0.25)', border: '2px solid #D4AF37', borderRadius: '16px', padding: '24px', textAlign: 'center'}}>
                    <div style={{fontSize: '36px'}}>🔥</div>
                    <p style={{fontSize: '28px', fontWeight: 'bold', color: '#D4AF37'}}>{streak}</p>
                    <p style={{fontSize: '13px', color: '#F4E5C2'}}>يوم</p>
                  </div>
                  <div style={{background: 'rgba(212, 175, 55, 0.25)', border: '2px solid #D4AF37', borderRadius: '16px', padding: '24px', textAlign: 'center'}}>
                    <div style={{fontSize: '36px'}}>🏆</div>
                    <p style={{fontSize: '28px', fontWeight: 'bold', color: '#D4AF37'}}>{points}</p>
                    <p style={{fontSize: '13px', color: '#F4E5C2'}}>نقطة</p>
                  </div>
                  <div style={{background: 'rgba(212, 175, 55, 0.25)', border: '2px solid #D4AF37', borderRadius: '16px', padding: '24px', textAlign: 'center'}}>
                    <div style={{fontSize: '36px'}}>✅</div>
                    <p style={{fontSize: '28px', fontWeight: 'bold', color: '#D4AF37'}}>{Object.keys(habitsDone).length}</p>
                    <p style={{fontSize: '13px', color: '#F4E5C2'}}>عادات</p>
                  </div>
                </div>
              </div>
            </div>
          );

          const DashboardScreen = () => {
            const handlePhotoUpload = (e) => {
              const file = e.target.files[0];
              if (file) {
                const reader = new FileReader();
                reader.onloadend = () => {
                  const newPhoto = {
                    image: reader.result,
                    date: new Date().toLocaleDateString('ar-SA'),
                    weight: userProfile.healthData.weight
                  };
                  setPreviousPhotos([...previousPhotos, newPhoto]);
                  setDailyPhoto(reader.result);
                  setPoints(prev => prev + 10);
                  setStreak(prev => prev + 1);
                  setTimeout(() => {
                    const weightChange = previousPhotos.length > 0 ? 
                      (userProfile.healthData.weight - previousPhotos[0].weight).toFixed(1) : 0;
                    setPhotoAnalysis({
                      weight: userProfile.healthData.weight + ' كجم',
                      change: weightChange > 0 ? `+${weightChange} كجم` : `${weightChange} كجم`,
                      trend: weightChange > 0 ? 'زيادة' : weightChange < 0 ? 'نقصان' : 'ثابت',
                      advice: weightChange > 0 ? 'حاول تقليل السعرات' : weightChange < 0 ? 'استمر! تقدم ممتاز' : 'حافظ على نظامك',
                      photosCount: previousPhotos.length + 1
                    });
                  }, 1500);
                };
                reader.readAsDataURL(file);
              }
            };

            const handleComplete = (id, pts) => {
              if (!habitsDone[id]) {
                setHabitsDone({...habitsDone, [id]: true});
                setPoints(prev => prev + pts);
                if (id === 2) setSteps(10000);
              }
            };

            const handleMedicationTaken = (med) => {
              if (!medicationsTaken[med]) {
                setMedicationsTaken({...medicationsTaken, [med]: true});
                setPoints(prev => prev + 10);
              }
            };

            const handleAddMeasurement = () => {
              if (currentMeasurement.weight && currentMeasurement.waist && currentMeasurement.chest) {
                const newMeasurement = {
                  ...currentMeasurement,
                  date: new Date().toLocaleDateString('ar-SA'),
                  timestamp: Date.now()
                };
                setBodyMeasurements([...bodyMeasurements, newMeasurement]);
                setPoints(prev => prev + 20);
                setStreak(prev => prev + 1);
                setShowMeasurementForm(false);
                setCurrentMeasurement({weight: '', waist: '', chest: '', arms: '', hips: ''});
              }
            };

            const membership = getMembershipLevel(points);
            const latestMeasurement = bodyMeasurements[bodyMeasurements.length - 1];
            const firstMeasurement = bodyMeasurements[0];
            const hasProgress = bodyMeasurements.length > 1;
            const weightChange = hasProgress ? (parseFloat(latestMeasurement.weight) - parseFloat(firstMeasurement.weight)).toFixed(1) : 0;

            return (
              <div style={{minHeight: '100vh', background: 'linear-gradient(135deg, #0a0e27 0%, #16213e 50%, #0a0e27 100%)', padding: '24px', position: 'relative'}}>
                <div style={{position: 'fixed', top: 0, left: 0, width: '100%', height: '100%', pointerEvents: 'none', zIndex: 0}}>
                  {[...Array(60)].map((_, i) => (
                    <div key={i} style={{position: 'absolute', width: '2px', height: '2px', background: '#D4AF37', borderRadius: '50%', left: Math.random() * 100 + '%', top: Math.random() * 100 + '%', animation: `twinkle ${2 + Math.random() * 3}s infinite`}}></div>
                  ))}
                </div>
                <div style={{maxWidth: '960px', margin: '0 auto', position: 'relative', zIndex: 10}}>
                  {showProfile && <ProfileModal />}
                  
                  <div style={{background: `linear-gradient(135deg, ${membership.color}33 0%, ${membership.color}22 100%)`, border: `2px solid ${membership.color}`, borderRadius: '20px', padding: '20px', marginBottom: '24px', display: 'flex', justifyContent: 'space-between', alignItems: 'center'}}>
                    <div style={{display: 'flex', alignItems: 'center', gap: '16px'}}>
                      <div style={{fontSize: '48px'}}>{membership.icon}</div>
                      <div>
                        <h3 style={{color: '#F4E5C2', fontSize: '20px', fontWeight: 'bold', marginBottom: '4px'}}>عضوية {membership.level}</h3>
                        <p style={{color: membership.color, fontSize: '15px', fontWeight: '600'}}>🎁 {membership.reward}</p>
                      </div>
                    </div>
                    <div style={{textAlign: 'center'}}>
                      <p style={{color: '#F4E5C2', fontSize: '14px', marginBottom: '4px'}}>نقاطك</p>
                      <p style={{color: membership.color, fontSize: '32px', fontWeight: 'bold', fontFamily: 'Amiri, serif'}}>{points}</p>
                    </div>
                  </div>

                  {notifications.length > 0 && (
                    <div style={{marginBottom: '24px'}}>
                      {notifications.map(notif => (
                        <div key={notif.id} style={{background: 'rgba(212, 175, 55, 0.2)', border: '2px solid #D4AF37', borderRadius: '16px', padding: '16px 20px', marginBottom: '12px', display: 'flex', justifyContent: 'space-between', alignItems: 'center', backdropFilter: 'blur(10px)'}}>
                          <div style={{display: 'flex', alignItems: 'center', gap: '16px'}}>
                            <span style={{fontSize: '24px'}}>⚡️</span>
                            <span style={{color: '#F4E5C2', fontWeight: '600', fontSize: '15px'}}>{notif.message}</span>
                          </div>
                          <span style={{color: '#D4AF37', fontSize: '14px', fontWeight: '600'}}>{notif.time}</span>
                        </div>
                      ))}
                    </div>
                  )}
                  
                  <div style={{background: 'linear-gradient(135deg, #2a2418 0%, #3d3124 100%)', border: '2px solid #D4AF37', borderRadius: '24px', padding: '28px', marginBottom: '28px', backdropFilter: 'blur(20px)', boxShadow: '0 10px 40px rgba(0, 0, 0, 0.3)'}}>
                    <div style={{display: 'flex', justifyContent: 'space-between', alignItems: 'center'}}>
                      <div>
                        <h1 onClick={() => setShowProfile(true)} style={{fontFamily: 'Amiri, serif', fontSize: '28px', fontWeight: 'bold', color: '#D4AF37', marginBottom: '10px', cursor: 'pointer', textDecoration: 'underline'}}>{userProfile.name}</h1>
                        <select value={selectedLocation} onChange={(e) => setSelectedLocation(e.target.value)} style={{background: 'transparent', color: '#F4E5C2', border: 'none', fontSize: '15px', cursor: 'pointer', outline: 'none', fontWeight: '500'}}>
                          <option style={{background: '#2a2418', color: '#F4E5C2'}}>المنزل</option>
                          <option style={{background: '#2a2418', color: '#F4E5C2'}}>العمل</option>
                          <option style={{background: '#2a2418', color: '#F4E5C2'}}>الجيم</option>
                        </select>
                      </div>
                      <div style={{display: 'flex', gap: '12px'}}>
                        <button onClick={() => setCurrentStep('group')} style={{padding: '14px 16px', background: 'rgba(212, 175, 55, 0.25)', border: '2px solid #D4AF37', borderRadius: '14px', color: '#D4AF37', cursor: 'pointer', fontSize: '22px'}}>👥</button>
                        <button onClick={() => setCurrentStep('coach')} style={{padding: '14px 16px', background: 'rgba(212, 175, 55, 0.25)', border: '2px solid #D4AF37', borderRadius: '14px', color: '#D4AF37', cursor: 'pointer', position: 'relative', fontSize: '22px'}}>
                          <span style={{position: 'absolute', top: '8px', right: '8px', width: '10px', height: '10px', background: '#D4AF37', borderRadius: '50%', boxShadow: '0 0 10px #D4AF37'}}></span>🧠
                        </button>
                      </div>
                    </div>
                  </div>
                  
                  <div style={{display: 'grid', gridTemplateColumns: 'repeat(3, 1fr)', gap: '16px', marginBottom: '28px'}}>
                    <div style={{background: 'rgba(212, 175, 55, 0.2)', border: '2px solid #D4AF37', borderRadius: '20px', padding: '24px', backdropFilter: 'blur(10px)', textAlign: 'center'}}>
                      <div style={{fontSize: '32px'}}>⚡️</div>
                      <p style={{fontSize: '28px', fontWeight: 'bold', color: '#D4AF37'}}>{streak}</p>
                      <p style={{fontSize: '13px', color: '#F4E5C2'}}>يوم</p>
                    </div>
                    <div style={{background: 'rgba(212, 175, 55, 0.2)', border: '2px solid #D4AF37', borderRadius: '20px', padding: '24px', backdropFilter: 'blur(10px)', textAlign: 'center'}}>
                      <div style={{fontSize: '32px'}}>🏆</div>
                      <p style={{fontSize: '28px', fontWeight: 'bold', color: '#D4AF37'}}>{points}</p>
                      <p style={{fontSize: '13px', color: '#F4E5C2'}}>نقطة</p>
                    </div>
                    <div style={{background: 'rgba(212, 175, 55, 0.2)', border: '2px solid #D4AF37', borderRadius: '20px', padding: '24px', backdropFilter: 'blur(10px)', textAlign: 'center'}}>
                      <div style={{fontSize: '32px'}}>👟</div>
                      <p style={{fontSize: '28px', fontWeight: 'bold', color: '#D4AF37'}}>{steps}</p>
                      <p style={{fontSize: '13px', color: '#F4E5C2'}}>خطوة</p>
                    </div>
                  </div>
                  
                  {userProfile.medications && userProfile.medications.length > 0 && (
                    <div style={{marginBottom: '28px'}}>
                      <h3 style={{fontFamily: 'Amiri, serif', fontSize: '24px', fontWeight: 'bold', color: '#D4AF37', marginBottom: '20px'}}>💊 أدويتك</h3>
                      {userProfile.medications.map((med, i) => (
                        <div key={i} style={{background: medicationsTaken[med] ? 'rgba(212, 175, 55, 0.3)' : 'linear-gradient(135deg, #2a2418 0%, #3d3124 100%)', border: '2px solid #D4AF37', borderRadius: '20px', padding: '24px', marginBottom: '16px', display: 'flex', justifyContent: 'space-between', alignItems: 'center', backdropFilter: 'blur(10px)'}}>
                          <div style={{display: 'flex', gap: '20px', alignItems: 'center'}}>
                            <div style={{fontSize: '48px'}}>💊</div>
                            <div>
                              <h4 style={{fontWeight: 'bold', color: '#F4E5C2', fontSize: '20px'}}>{med}</h4>
                              <p style={{color: '#C9A961', fontSize: '14px'}}>علاج يومي</p>
                            </div>
                          </div>
                          <div style={{textAlign: 'center'}}>
                            <p style={{color: '#D4AF37', fontWeight: 'bold', fontSize: '18px'}}>+10</p>
                            <button onClick={() => handleMedicationTaken(med)} disabled={medicationsTaken[med]} style={{padding: '10px 20px', background: medicationsTaken[med] ? 'linear-gradient(135deg, #D4AF37 0%, #C9A961 100%)' : 'rgba(212, 175, 55, 0.25)', border: '2px solid #D4AF37', borderRadius: '12px', color: medicationsTaken[med] ? '#1a1410' : '#D4AF37', fontWeight: '700', cursor: medicationsTaken[med] ? 'not-allowed' : 'pointer', marginTop: '8px'}}>{medicationsTaken[med] ? '✓ تم' : 'أخذت'}</button>
                          </div>
                        </div>
                      ))}
                    </div>
                  )}
                  
                  <h3 style={{fontFamily: 'Amiri, serif', fontSize: '24px', fontWeight: 'bold', color: '#D4AF37', marginBottom: '20px'}}>🎯 مهام اليوم</h3>
                  {habits.map(habit => (
                    <div key={habit.id} style={{background: habitsDone[habit.id] ? 'rgba(212, 175, 55, 0.3)' : 'linear-gradient(135deg, #2a2418 0%, #3d3124 100%)', border: '2px solid #D4AF37', borderRadius: '20px', padding: '24px', marginBottom: '16px', display: 'flex', justifyContent: 'space-between', alignItems: 'center', backdropFilter: 'blur(10px)'}}>
                      <div style={{display: 'flex', gap: '20px', alignItems: 'center', flex: 1}}>
                        <div style={{fontSize: '48px'}}>{habit.icon}</div>
                        <div style={{flex: 1}}>
                          <h4 style={{fontWeight: 'bold', color: '#F4E5C2', fontSize: '20px', marginBottom: '4px'}}>{habit.name}</h4>
                          <p style={{color: '#C9A961', fontSize: '14px', marginBottom: '8px'}}>{habit.type === 'positive' ? 'عادة إيجابية' : 'تجنب'}</p>
                          {habit.id === 2 && (
                            <div style={{marginTop: '8px'}}>
                              <div style={{background: 'rgba(15, 15, 10, 0.5)', height: '8px', borderRadius: '10px', overflow: 'hidden', marginBottom: '4px'}}>
                                <div style={{background: 'linear-gradient(90deg, #D4AF37 0%, #C9A961 100%)', height: '100%', width: `${(steps / 10000) * 100}%`, transition: 'width 0.3s'}}></div>
                              </div>
                              <p style={{color: '#D4AF37', fontSize: '12px', fontWeight: '600'}}>{steps} / 10,000 خطوة</p>
                            </div>
                          )}
                        </div>
                      </div>
                      <div style={{textAlign: 'center'}}>
                        <p style={{color: '#D4AF37', fontWeight: 'bold', fontSize: '18px'}}>+{habit.points}</p>
                        <button onClick={() => handleComplete(habit.id, habit.points)} disabled={habitsDone[habit.id]} style={{padding: '10px 20px', background: habitsDone[habit.id] ? 'linear-gradient(135deg, #D4AF37 0%, #C9A961 100%)' : 'rgba(212, 175, 55, 0.25)', border: '2px solid #D4AF37', borderRadius: '12px', color: habitsDone[habit.id] ? '#1a1410' : '#D4AF37', fontWeight: '700', cursor: habitsDone[habit.id] ? 'not-allowed' : 'pointer', marginTop: '8px', whiteSpace: 'nowrap'}}>{habitsDone[habit.id] ? '✓ تم' : 'تم'}</button>
                      </div>
                    </div>
                  ))}
                  
                  {/* قسم الصورة */}
                  <div style={{background: 'linear-gradient(135deg, #2a2418 0%, #3d3124 100%)', border: '2px solid #D4AF37', borderRadius: '24px', padding: '32px', marginTop: '28px', backdropFilter: 'blur(10px)', boxShadow: '0 10px 40px rgba(212, 175, 55, 0.2)'}}>
                    <div style={{display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: '20px'}}>
                      <h3 style={{fontFamily: 'Amiri, serif', fontSize: '24px', fontWeight: 'bold', color: '#D4AF37', display: 'flex', alignItems: 'center', gap: '12px'}}>
                        📸 تتبع التغيرات
                        <span style={{fontSize: '13px', background: 'rgba(212, 175, 55, 0.25)', color: '#D4AF37', padding: '6px 12px', borderRadius: '20px', fontWeight: '600', border: '1px solid #D4AF37'}}>تحليل ذكي</span>
                      </h3>
                      <span style={{background: 'linear-gradient(135deg, #D4AF37 0%, #C9A961 100%)', color: '#1a1410', padding: '6px 16px', borderRadius: '20px', fontSize: '15px', fontWeight: 'bold', boxShadow: '0 4px 15px rgba(212, 175, 55, 0.4)'}}>+10</span>
                    </div>
                    <p style={{color: '#F4E5C2', fontSize: '15px', marginBottom: '20px', lineHeight: '1.6'}}>التقط صورة لجسمك لتتبع التغيرات في الوزن والشكل عبر الوقت</p>
                    <input type="file" ref={dailyPhotoRef} onChange={handlePhotoUpload} accept="image/*" style={{display: 'none'}} />
                    <button onClick={() => dailyPhotoRef.current.click()} style={{width: '100%', height: '200px', background: dailyPhoto ? 'transparent' : 'rgba(15, 15, 10, 0.4)', border: '3px dashed #D4AF37', borderRadius: '16px', display: 'flex', alignItems: 'center', justifyContent: 'center', cursor: 'pointer', marginBottom: '20px', overflow: 'hidden'}}>
                      {dailyPhoto ? (
                        <img src={dailyPhoto} alt="Progress" style={{width: '100%', height: '100%', objectFit: 'cover', borderRadius: '16px'}} />
                      ) : (
                        <div style={{textAlign: 'center', color: '#D4AF37'}}>
                          <div style={{fontSize: '52px', marginBottom: '12px'}}>📷</div>
                          <span style={{fontWeight: '600', fontSize: '16px'}}>التقط صورة لتتبع تقدمك</span>
                        </div>
                      )}
                    </button>
                    
                    {photoAnalysis && (
                      <div style={{background: 'rgba(15, 15, 10, 0.5)', borderRadius: '16px', padding: '24px', border: '2px solid rgba(212, 175, 55, 0.5)', marginBottom: '20px'}}>
                        <h4 style={{fontFamily: 'Amiri, serif', fontWeight: 'bold', color: '#D4AF37', marginBottom: '16px', fontSize: '18px'}}>📊 تحليل التغيرات</h4>
                        <div style={{fontSize: '15px'}}>
                          <div style={{display: 'flex', justifyContent: 'space-between', marginBottom: '12px', paddingBottom: '12px', borderBottom: '1px solid rgba(212, 175, 55, 0.3)'}}>
                            <span style={{color: '#F4E5C2'}}>الوزن الحالي:</span>
                            <span style={{color: '#D4AF37', fontWeight: '600'}}>{photoAnalysis.weight}</span>
                          </div>
                          <div style={{display: 'flex', justifyContent: 'space-between', marginBottom: '12px', paddingBottom: '12px', borderBottom: '1px solid rgba(212, 175, 55, 0.3)'}}>
                            <span style={{color: '#F4E5C2'}}>التغير:</span>
                            <span style={{color: photoAnalysis.change.includes('-') ? '#22c55e' : '#ef4444', fontWeight: '600'}}>{photoAnalysis.change}</span>
                          </div>
                          <div style={{display: 'flex', justifyContent: 'space-between', marginBottom: '16px', paddingBottom: '16px', borderBottom: '1px solid rgba(212, 175, 55, 0.3)'}}>
                            <span style={{color: '#F4E5C2'}}>الاتجاه:</span>
                            <span style={{color: '#D4AF37', fontWeight: '600'}}>{photoAnalysis.trend}</span>
                          </div>
                          <div style={{background: 'rgba(212, 175, 55, 0.15)', padding: '16px', borderRadius: '12px', border: '1px solid rgba(212, 175, 55, 0.3)'}}>
                            <p style={{color: '#F4E5C2', fontSize: '14px', marginBottom: '8px', fontWeight: '600'}}>💡 نصيحة:</p>
                            <p style={{color: '#D4AF37', fontSize: '15px'}}>{photoAnalysis.advice}</p>
                          </div>
                        </div>
                      </div>
                    )}
                    
                    {previousPhotos.length > 0 && (
                      <div>
                        <h4 style={{color: '#D4AF37', fontSize: '18px', fontWeight: 'bold', marginBottom: '16px'}}>📈 صورك السابقة ({previousPhotos.length})</h4>
                        <div style={{display: 'grid', gridTemplateColumns: 'repeat(auto-fill, minmax(120px, 1fr))', gap: '12px'}}>
                          {previousPhotos.slice(-6).map((photo, idx) => (
                            <div key={idx} style={{position: 'relative', borderRadius: '12px', overflow: 'hidden', border: '2px solid rgba(212, 175, 55, 0.4)'}}>
                              <img src={photo.image} alt={`Day ${idx + 1}`} style={{width: '100%', height: '120px', objectFit: 'cover'}} />
                              <div style={{position: 'absolute', bottom: 0, left: 0, right: 0, background: 'rgba(0,0,0,0.7)', padding: '8px', textAlign: 'center'}}>
                                <p style={{color: '#D4AF37', fontSize: '11px', fontWeight: '600'}}>{photo.date}</p>
                              </div>
                            </div>
                          ))}
                        </div>
                      </div>
                    )}
                  </div>

                  {/* قسم Avatar */}
                  <div style={{background: 'linear-gradient(135deg, #2a2418 0%, #3d3124 100%)', border: '2px solid #D4AF37', borderRadius: '24px', padding: '32px', marginTop: '28px', backdropFilter: 'blur(10px)', boxShadow: '0 10px 40px rgba(212, 175, 55, 0.2)'}}>
                    <div style={{display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: '24px'}}>
                      <h3 style={{fontFamily: 'Amiri, serif', fontSize: '24px', fontWeight: 'bold', color: '#D4AF37', display: 'flex', alignItems: 'center', gap: '12px'}}>
                        🎯 مرآة التقدم الذكية
                        <span style={{fontSize: '13px', background: 'rgba(212, 175, 55, 0.25)', color: '#D4AF37', padding: '6px 12px', borderRadius: '20px', fontWeight: '600', border: '1px solid #D4AF37'}}>Avatar 3D</span>
                      </h3>
                      <span style={{background: 'linear-gradient(135deg, #D4AF37 0%, #C9A961 100%)', color: '#1a1410', padding: '6px 16px', borderRadius: '20px', fontSize: '15px', fontWeight: 'bold', boxShadow: '0 4px 15px rgba(212, 175, 55, 0.4)'}}>+20</span>
                    </div>

                    {!showMeasurementForm && bodyMeasurements.length === 0 && (
                      <button onClick={() => setShowMeasurementForm(true)} style={{width: '100%', padding: '80px 20px', background: 'rgba(15, 15, 10, 0.4)', border: '3px dashed #D4AF37', borderRadius: '16px', cursor: 'pointer', color: '#D4AF37', fontSize: '18px', fontWeight: '600'}}>
                        <div style={{fontSize: '60px', marginBottom: '16px'}}>📏</div>
                        أضف قياساتك الأولى
                      </button>
                    )}

                    {showMeasurementForm && (
                      <div style={{background: 'rgba(15, 15, 10, 0.5)', borderRadius: '16px', padding: '24px', border: '2px solid rgba(212, 175, 55, 0.5)', marginBottom: '20px'}}>
                        <h4 style={{color: '#D4AF37', fontSize: '18px', fontWeight: 'bold', marginBottom: '20px'}}>📊 أدخل قياساتك</h4>
                        <div style={{display: 'grid', gridTemplateColumns: 'repeat(2, 1fr)', gap: '16px', marginBottom: '16px'}}>
                          <div>
                            <label style={{display: 'block', color: '#F4E5C2', fontSize: '14px', marginBottom: '8px'}}>الوزن (كجم)</label>
                            <input type="number" value={currentMeasurement.weight} onChange={(e) => setCurrentMeasurement({...currentMeasurement, weight: e.target.value})} placeholder="70" style={{width: '100%', background: 'rgba(15, 15, 10, 0.5)', border: '2px solid rgba(212, 175, 55, 0.4)', borderRadius: '12px', padding: '12px', color: '#F4E5C2', outline: 'none'}} />
                          </div>
                          <div>
                            <label style={{display: 'block', color: '#F4E5C2', fontSize: '14px', marginBottom: '8px'}}>محيط الخصر (سم)</label>
                            <input type="number" value={currentMeasurement.waist} onChange={(e) => setCurrentMeasurement({...currentMeasurement, waist: e.target.value})} placeholder="80" style={{width: '100%', background: 'rgba(15, 15, 10, 0.5)', border: '2px solid rgba(212, 175, 55, 0.4)', borderRadius: '12px', padding: '12px', color: '#F4E5C2', outline: 'none'}} />
                          </div>
                          <div>
                            <label style={{display: 'block', color: '#F4E5C2', fontSize: '14px', marginBottom: '8px'}}>محيط الصدر (سم)</label>
                            <input type="number" value={currentMeasurement.chest} onChange={(e) => setCurrentMeasurement({...currentMeasurement, chest: e.target.value})} placeholder="95" style={{width: '100%', background: 'rgba(15, 15, 10, 0.5)', border: '2px solid rgba(212, 175, 55, 0.4)', borderRadius: '12px', padding: '12px', color: '#F4E5C2', outline: 'none'}} />
                          </div>
                          <div>
                            <label style={{display: 'block', color: '#F4E5C2', fontSize: '14px', marginBottom: '8px'}}>محيط الذراع (سم)</label>
                            <input type="number" value={currentMeasurement.arms} onChange={(e) => setCurrentMeasurement({...currentMeasurement, arms: e.target.value})} placeholder="30" style={{width: '100%', background: 'rgba(15, 15, 10, 0.5)', border: '2px solid rgba(212, 175, 55, 0.4)', borderRadius: '12px', padding: '12px', color: '#F4E5C2', outline: 'none'}} />
                          </div>
                          <div style={{gridColumn: 'span 2'}}>
                            <label style={{display: 'block', color: '#F4E5C2', fontSize: '14px', marginBottom: '8px'}}>محيط الوركين (سم)</label>
                            <input type="number" value={currentMeasurement.hips} onChange={(e) => setCurrentMeasurement({...currentMeasurement, hips: e.target.value})} placeholder="100" style={{width: '100%', background: 'rgba(15, 15, 10, 0.5)', border: '2px solid rgba(212, 175, 55, 0.4)', borderRadius: '12px', padding: '12px', color: '#F4E5C2', outline: 'none'}} />
                          </div>
                        </div>
                        <div style={{display: 'flex', gap: '12px'}}>
                          <button onClick={handleAddMeasurement} disabled={!currentMeasurement.weight || !currentMeasurement.waist || !currentMeasurement.chest} style={{flex: 1, padding: '14px', background: (currentMeasurement.weight && currentMeasurement.waist && currentMeasurement.chest) ? 'linear-gradient(135deg, #D4AF37 0%, #C9A961 100%)' : 'rgba(80, 80, 80, 0.4)', border: 'none', borderRadius: '12px', color: (currentMeasurement.weight && currentMeasurement.waist && currentMeasurement.chest) ? '#1a1410' : '#888', fontWeight: '700', cursor: (currentMeasurement.weight && currentMeasurement.waist && currentMeasurement.chest) ? 'pointer' : 'not-allowed'}}>✓ حفظ القياسات</button>
                          <button onClick={() => setShowMeasurementForm(false)} style={{padding: '14px 20px', background: 'rgba(212, 175, 55, 0.2)', border: '2px solid #D4AF37', borderRadius: '12px', color: '#D4AF37', fontWeight: '700', cursor: 'pointer'}}>إلغاء</button>
                        </div>
                      </div>
                    )}

                    {latestMeasurement && (
                      <div>
                        <div style={{marginBottom: '32px'}}>
                          {hasProgress && !showComparison && (
                            <button onClick={() => setShowComparison(true)} style={{width: '100%', padding: '14px', background: 'rgba(212, 175, 55, 0.2)', border: '2px solid #D4AF37', borderRadius: '12px', color: '#D4AF37', fontWeight: '600', cursor: 'pointer', marginBottom: '24px', fontSize: '16px'}}>
                              👀 شاهد التحول (قبل وبعد)
                            </button>
                          )}
                          {hasProgress && showComparison && (
                            <button onClick={() => setShowComparison(false)} style={{width: '100%', padding: '14px', background: 'rgba(212, 175, 55, 0.2)', border: '2px solid #D4AF37', borderRadius: '12px', color: '#D4AF37', fontWeight: '600', cursor: 'pointer', marginBottom: '24px', fontSize: '16px'}}>
                              ✕ إخفاء المقارنة
                            </button>
                          )}

                          {showComparison && hasProgress ? (
                            <div style={{display: 'grid', gridTemplateColumns: '1fr 1fr', gap: '24px', marginBottom: '24px'}}>
                              <div>
                                <h4 style={{color: '#94A3B8', textAlign: 'center', marginBottom: '16px', fontSize: '16px', fontWeight: 'bold'}}>قبل</h4>
                                <BodyAvatar measurement={firstMeasurement} isOld={true} />
                              </div>
                              <div>
                                <h4 style={{color: '#D4AF37', textAlign: 'center', marginBottom: '16px', fontSize: '16px', fontWeight: 'bold'}}>الآن</h4>
                                <BodyAvatar measurement={latestMeasurement} showMedal={weightChange < -5} />
                              </div>
                            </div>
                          ) : (
                            <BodyAvatar measurement={latestMeasurement} showMedal={weightChange < -5} />
                          )}
                        </div>

                        <div style={{background: 'rgba(15, 15, 10, 0.5)', borderRadius: '16px', padding: '24px', border: '2px solid rgba(212, 175, 55, 0.5)', marginBottom: '20px'}}>
                          <h4 style={{fontFamily: 'Amiri, serif', fontWeight: 'bold', color: '#D4AF37', marginBottom: '16px', fontSize: '18px'}}>📊 إحصائياتك</h4>
                          <div style={{display: 'grid', gridTemplateColumns: 'repeat(2, 1fr)', gap: '12px'}}>
                            <div style={{background: 'rgba(212, 175, 55, 0.1)', padding: '16px', borderRadius: '12px', border: '1px solid rgba(212, 175, 55, 0.3)'}}>
                              <p style={{color: '#C9A961', fontSize: '13px', marginBottom: '4px'}}>الوزن</p>
                              <p style={{color: '#D4AF37', fontSize: '20px', fontWeight: 'bold'}}>{latestMeasurement.weight} كجم</p>
                            </div>
                            <div style={{background: 'rgba(212, 175, 55, 0.1)', padding: '16px', borderRadius: '12px', border: '1px solid rgba(212, 175, 55, 0.3)'}}>
                              <p style={{color: '#C9A961', fontSize: '13px', marginBottom: '4px'}}>الخصر</p>
                              <p style={{color: '#D4AF37', fontSize: '20px', fontWeight: 'bold'}}>{latestMeasurement.waist} سم</p>
                            </div>
                            <div style={{background: 'rgba(212, 175, 55, 0.1)', padding: '16px', borderRadius: '12px', border: '1px solid rgba(212, 175, 55, 0.3)'}}>
                              <p style={{color: '#C9A961', fontSize: '13px', marginBottom: '4px'}}>الصدر</p>
                              <p style={{color: '#D4AF37', fontSize: '20px', fontWeight: 'bold'}}>{latestMeasurement.chest} سم</p>
                            </div>
                            {hasProgress && (
                              <div style={{background: weightChange < 0 ? 'rgba(34, 197, 94, 0.1)' : 'rgba(239, 68, 68, 0.1)', padding: '16px', borderRadius: '12px', border: weightChange < 0 ? '1px solid rgba(34, 197, 94, 0.3)' : '1px solid rgba(239, 68, 68, 0.3)'}}>
                                <p style={{color: '#C9A961', fontSize: '13px', marginBottom: '4px'}}>التغير</p>
                                <p style={{color: weightChange < 0 ? '#22c55e' : '#ef4444', fontSize: '20px', fontWeight: 'bold'}}>{weightChange > 0 ? '+' : ''}{weightChange} كجم</p>
                              </div>
                            )}
                          </div>
                        </div>

                        <button onClick={() => setShowMeasurementForm(true)} style={{width: '100%', padding: '14px', background: 'rgba(212, 175, 55, 0.2)', border: '2px solid #D4AF37', borderRadius: '12px', color: '#D4AF37', fontWeight: '700', cursor: 'pointer', fontSize: '16px'}}>
                          + تحديث القياسات
                        </button>
                      </div>
                    )}
                  </div>
                </div>
              </div>
            );
          };

          const GroupScreen = () => {
            const members = [{id:1,pts:312,eff:89},{id:2,pts:245,eff:78},{id:3,pts:points,eff:67,me:true},{id:4,pts:198,eff:65},{id:5,pts:156,eff:54}];
            return (
              <div style={{minHeight: '100vh', background: 'linear-gradient(135deg, #0a0e27 0%, #16213e 50%, #0a0e27 100%)', padding: '24px', position: 'relative'}}>
                <div style={{position: 'fixed', top: 0, left: 0, width: '100%', height: '100%', pointerEvents: 'none', zIndex: 0}}>
                  {[...Array(50)].map((_, i) => (
                    <div key={i} style={{position: 'absolute', width: '2px', height: '2px', background: '#D4AF37', borderRadius: '50%', left: Math.random() * 100 + '%', top: Math.random() * 100 + '%', animation: `twinkle ${2 + Math.random() * 3}s infinite`}}></div>
                  ))}
                </div>
                <div style={{maxWidth: '960px', margin: '0 auto', position: 'relative', zIndex: 10}}>
                  <button onClick={() => setCurrentStep('dashboard')} style={{marginBottom: '32px', display: 'flex', alignItems: 'center', gap: '10px', color: '#D4AF37', background: 'none', border: 'none', cursor: 'pointer', fontSize: '17px', fontWeight: '600'}}>← رجوع</button>
                  <h2 style={{fontFamily: 'Amiri, serif', fontSize: '40px', fontWeight: 'bold', color: '#D4AF37', textAlign: 'center', marginBottom: '16px'}}>المنافسين</h2>
                  <p style={{textAlign: 'center', color: '#F4E5C2', marginBottom: '40px', fontSize: '16px'}}>اجمع النقاط واحصل على مكافآت رائعة! 🎁</p>
                  
                  <div style={{display: 'grid', gridTemplateColumns: 'repeat(auto-fit, minmax(280px, 1fr))', gap: '16px', marginBottom: '32px'}}>
                    <div style={{background: 'rgba(205, 127, 50, 0.2)', border: '2px solid #CD7F32', borderRadius: '16px', padding: '20px', textAlign: 'center'}}>
                      <div style={{fontSize: '40px', marginBottom: '8px'}}>🥉</div>
                      <h3 style={{color: '#CD7F32', fontSize: '20px', fontWeight: 'bold', marginBottom: '8px'}}>برونزي</h3>
                      <p style={{color: '#F4E5C2', fontSize: '14px', marginBottom: '8px'}}>200+ نقطة</p>
                      <p style={{color: '#C9A961', fontSize: '13px'}}>خصم على الموقع</p>
                    </div>
                    <div style={{background: 'rgba(192, 192, 192, 0.2)', border: '2px solid #C0C0C0', borderRadius: '16px', padding: '20px', textAlign: 'center'}}>
                      <div style={{fontSize: '40px', marginBottom: '8px'}}>🥈</div>
                      <h3 style={{color: '#C0C0C0', fontSize: '20px', fontWeight: 'bold', marginBottom: '8px'}}>فضي</h3>
                      <p style={{color: '#F4E5C2', fontSize: '14px', marginBottom: '8px'}}>500+ نقطة</p>
                      <p style={{color: '#C9A961', fontSize: '13px'}}>اشتراك أكل صحي</p>
                    </div>
                    <div style={{background: 'rgba(212, 175, 55, 0.2)', border: '2px solid #D4AF37', borderRadius: '16px', padding: '20px', textAlign: 'center', animation: 'pulse 2s infinite'}}>
                      <div style={{fontSize: '40px', marginBottom: '8px'}}>🥇</div>
                      <h3 style={{color: '#D4AF37', fontSize: '20px', fontWeight: 'bold', marginBottom: '8px'}}>ذهبي</h3>
                      <p style={{color: '#F4E5C2', fontSize: '14px', marginBottom: '8px'}}>1000+ نقطة</p>
                      <p style={{color: '#C9A961', fontSize: '13px'}}>اشتراك جيم مجاني</p>
                    </div>
                  </div>

                  <h3 style={{fontFamily: 'Amiri, serif', fontSize: '28px', fontWeight: 'bold', color: '#D4AF37', marginBottom: '24px'}}>🏅 الترتيب</h3>
                  {members.sort((a,b)=>b.pts-a.pts).map((m,i)=>{
                    const memberLevel = getMembershipLevel(m.pts);
                    return (
                      <div key={m.id} style={{background: m.me ? 'rgba(212, 175, 55, 0.3)' : 'linear-gradient(135deg, #2a2418 0%, #3d3124 100%)', border: m.me ? '3px solid #D4AF37' : '2px solid rgba(212, 175, 55, 0.3)', borderRadius: '20px', padding: '28px', marginBottom: '16px', display: 'flex', justifyContent: 'space-between', backdropFilter: 'blur(10px)', boxShadow: m.me ? '0 8px 30px rgba(212, 175, 55, 0.4)' : 'none'}}>
                        <div style={{display: 'flex', gap: '20px', alignItems: 'center', flex: 1}}>
                          <div style={{position: 'relative', fontSize: '44px'}}>
                            👤
                            {i===0&&<span style={{position: 'absolute', top: '-10px', right: '-10px', fontSize: '28px'}}>👑</span>}
                          </div>
                          <div style={{flex: 1}}>
                            <div style={{display: 'flex', alignItems: 'center', gap: '12px', marginBottom: '4px'}}>
                              <h3 style={{fontWeight: 'bold', color: '#F4E5C2', fontSize: '20px'}}>{m.me?'أنت':'عضو '+m.id}</h3>
                              <span style={{background: memberLevel.color + '33', color: memberLevel.color, padding: '4px 10px', borderRadius: '12px', fontSize: '12px', fontWeight: '700', border: `1px solid ${memberLevel.color}`}}>{memberLevel.icon} {memberLevel.level}</span>
                            </div>
                            <p style={{color: '#C9A961', fontSize: '15px'}}>جهد: {m.eff}%</p>
                          </div>
                        </div>
                        <div style={{textAlign: 'right'}}>
                          <p style={{color: '#D4AF37', fontWeight: 'bold', fontSize: '28px', fontFamily: 'Amiri, serif', marginBottom: '4px'}}>{m.pts}</p>
                          <p style={{color: '#F4E5C2', fontSize: '14px'}}>نقطة</p>
                        </div>
                      </div>
                    );
                  })}
                </div>
              </div>
            );
          };

          const CoachScreen = () => {
            const dietPlan = generateDietPlan();
            return (
              <div style={{minHeight: '100vh', background: 'linear-gradient(135deg, #0a0e27 0%, #16213e 50%, #0a0e27 100%)', padding: '24px', position: 'relative'}}>
                <div style={{position: 'fixed', top: 0, left: 0, width: '100%', height: '100%', pointerEvents: 'none', zIndex: 0}}>
                  {[...Array(50)].map((_, i) => (
                    <div key={i} style={{position: 'absolute', width: '2px', height: '2px', background: '#D4AF37', borderRadius: '50%', left: Math.random() * 100 + '%', top: Math.random() * 100 + '%', animation: `twinkle ${2 + Math.random() * 3}s infinite`}}></div>
                  ))}
                </div>
                <div style={{maxWidth: '960px', margin: '0 auto', position: 'relative', zIndex: 10}}>
                  <button onClick={() => setCurrentStep('dashboard')} style={{marginBottom: '32px', display: 'flex', alignItems: 'center', gap: '10px', color: '#D4AF37', background: 'none', border: 'none', cursor: 'pointer', fontSize: '17px', fontWeight: '600'}}>← رجوع</button>
                  <div style={{background: 'linear-gradient(135deg, #2a2418 0%, #3d3124 100%)', border: '2px solid #D4AF37', borderRadius: '32px', padding: '32px', backdropFilter: 'blur(20px)', boxShadow: '0 20px 50px rgba(0, 0, 0, 0.4), 0 0 60px rgba(212, 175, 55, 0.2)'}}>
                    <div style={{display: 'flex', gap: '24px', alignItems: 'flex-start', marginBottom: '32px'}}>
                      <div style={{background: 'linear-gradient(135deg, #D4AF37 0%, #C9A961 100%)', minWidth: '80px', height: '80px', borderRadius: '50%', display: 'flex', alignItems: 'center', justifyContent: 'center', fontSize: '40px', flexShrink: 0, boxShadow: '0 10px 30px rgba(212, 175, 55, 0.5)'}}>🧠</div>
                      <div style={{flex: 1}}>
                        <h3 style={{fontFamily: 'Amiri, serif', fontSize: '26px', fontWeight: 'bold', color: '#D4AF37', marginBottom: '16px'}}>💬 مستشارك الشخصي</h3>
                        <p style={{color: '#F4E5C2', lineHeight: '1.9', fontSize: '17px'}}>مرحباً {userProfile.name}! قمت بتحليل بياناتك الصحية وأعددت لك نظام غذائي مخصص يناسب احتياجاتك. 🎯</p>
                      </div>
                    </div>
                    
                    <div style={{background: 'rgba(15, 15, 10, 0.5)', borderRadius: '20px', padding: '28px', border: '2px solid rgba(212, 175, 55, 0.4)'}}>
                      <h4 style={{fontFamily: 'Amiri, serif', color: '#D4AF37', fontSize: '22px', fontWeight: 'bold', marginBottom: '20px', display: 'flex', alignItems: 'center', gap: '10px'}}>
                        🥗 نظامك الغذائي المخصص
                      </h4>
                      <div style={{color: '#F4E5C2', fontSize: '16px', lineHeight: '2', whiteSpace: 'pre-line'}}>
                        {dietPlan}
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            );
          };

          if (currentStep === 'onboarding') return <OnboardingScreen />;
          if (currentStep === 'group') return <GroupScreen />;
          if (currentStep === 'coach') return <CoachScreen />;
          return <DashboardScreen />;
        }

        ReactDOM.createRoot(document.getElementById('root')).render(<RhythmApp />);
    </script>
</body>
</html>
