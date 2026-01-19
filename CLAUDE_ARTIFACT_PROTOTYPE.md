# Heidi Health AI Medical Scribe - Claude Artifact Interactive Prototype

> **Instructions:** Copy the React code below into a Claude Artifact to create an interactive prototype of the Heidi Health interface. This prototype simulates all major features of the Heidi AI Medical Scribe platform.

---

## Prompt for Claude

```
Create an interactive React artifact that simulates the Heidi Health AI Medical Scribe interface. Include all the features shown in the code below.
```

---

## Interactive Prototype Code (React + Tailwind)

```tsx
import React, { useState, useEffect, useRef } from 'react';

// Main App Component
export default function HeidiHealthPrototype() {
  // State Management
  const [isRecording, setIsRecording] = useState(false);
  const [recordingTime, setRecordingTime] = useState(0);
  const [activeView, setActiveView] = useState('session');
  const [selectedTemplate, setSelectedTemplate] = useState('SOAP Note');
  const [brainMode, setBrainMode] = useState('standard');
  const [showTemplateModal, setShowTemplateModal] = useState(false);
  const [showFeatureDemo, setShowFeatureDemo] = useState(false);
  const [transcript, setTranscript] = useState([
    { speaker: 'Doctor', text: 'Good afternoon, Mr. Smith. How have you been feeling since your last visit?' },
    { speaker: 'Patient', text: 'Hi doctor. Overall pretty good, but I\'ve been having some occasional headaches in the morning.' },
    { speaker: 'Doctor', text: 'I see. Have you noticed any patterns? Are they related to stress or sleep?' },
    { speaker: 'Patient', text: 'They do seem worse when I don\'t sleep well. I\'ve been under stress at work lately.' },
  ]);
  const [generatedNote, setGeneratedNote] = useState(null);
  const [askHeidiInput, setAskHeidiInput] = useState('');
  const [notification, setNotification] = useState('');
  const [billingCodes, setBillingCodes] = useState([
    { type: 'ICD-10', code: 'G44.209', desc: 'Tension-type headache, unspecified', confirmed: true },
    { type: 'CPT', code: '99214', desc: 'Office visit, established patient', confirmed: true },
    { type: 'ICD-10', code: 'F43.0', desc: 'Acute stress reaction', confirmed: false },
  ]);
  const [recentSessions] = useState([
    { id: 1, name: 'John Smith - Follow Up', date: 'Today, 2:30 PM', active: true },
    { id: 2, name: 'Sarah Johnson - Initial Consult', date: 'Today, 11:00 AM', active: false },
    { id: 3, name: 'Michael Brown - Annual Physical', date: 'Yesterday', active: false },
    { id: 4, name: 'Emily Davis - Urgent Care', date: 'Jan 17, 2026', active: false },
  ]);

  // Recording Timer Effect
  useEffect(() => {
    let interval;
    if (isRecording) {
      interval = setInterval(() => {
        setRecordingTime(t => t + 1);
      }, 1000);
    }
    return () => clearInterval(interval);
  }, [isRecording]);

  // Notification Effect
  useEffect(() => {
    if (notification) {
      const timer = setTimeout(() => setNotification(''), 3000);
      return () => clearTimeout(timer);
    }
  }, [notification]);

  // Format time
  const formatTime = (seconds) => {
    const mins = Math.floor(seconds / 60).toString().padStart(2, '0');
    const secs = (seconds % 60).toString().padStart(2, '0');
    return `${mins}:${secs}`;
  };

  // Show notification
  const showNotif = (msg) => setNotification(msg);

  // Toggle Recording
  const toggleRecording = () => {
    if (!isRecording) {
      setIsRecording(true);
      showNotif('🎙️ Recording started');
      // Simulate new transcript entries
      setTimeout(() => {
        if (isRecording) {
          setTranscript(prev => [...prev,
            { speaker: 'Doctor', text: 'Let me check your blood pressure. It looks slightly elevated at 128 over 82.' }
          ]);
        }
      }, 3000);
    } else {
      setIsRecording(false);
      showNotif('✅ Recording stopped - Generating note...');
      generateNote();
    }
  };

  // Generate Note
  const generateNote = () => {
    setTimeout(() => {
      setGeneratedNote({
        subjective: `Patient is a 45-year-old male presenting for follow-up visit. Reports overall improvement in general health since last visit. Chief complaint today: occasional morning headaches.

HPI: Patient describes intermittent headaches occurring primarily in the morning upon waking. Headaches typically resolve after consuming caffeine. Reports correlation with poor sleep quality. Currently experiencing increased occupational stress.`,
        objective: `• Vitals: BP 128/82, HR 72, Temp 98.6°F
• General: Alert, oriented, no acute distress
• HEENT: Normocephalic, pupils equal and reactive
• Neuro: Cranial nerves II-XII intact`,
        assessment: `1. Tension-type headache, likely related to sleep hygiene and stress
2. Caffeine dependency
3. Work-related stress`,
        plan: `• Sleep hygiene counseling provided
• Recommend gradual caffeine reduction
• Consider referral to behavioral health if stress persists
• Follow up in 4 weeks or sooner if symptoms worsen
• Headache diary recommended`
      });
      showNotif('📝 Clinical note generated successfully!');
    }, 1500);
  };

  // Ask Heidi
  const handleAskHeidi = () => {
    if (askHeidiInput.trim()) {
      showNotif(`Processing: "${askHeidiInput}"`);
      setAskHeidiInput('');
      setTimeout(() => {
        showNotif('✅ Note updated based on your request!');
      }, 1500);
    }
  };

  // Confirm billing code
  const confirmCode = (index) => {
    const updated = [...billingCodes];
    updated[index].confirmed = !updated[index].confirmed;
    setBillingCodes(updated);
    showNotif(updated[index].confirmed ? '✅ Code confirmed' : 'Code unconfirmed');
  };

  // Templates
  const templates = [
    { name: 'SOAP Note', desc: 'Standard subjective, objective, assessment, plan format', favorite: true },
    { name: 'H&P', desc: 'Comprehensive history and physical examination', favorite: false },
    { name: 'Progress Note', desc: 'Brief follow-up documentation', favorite: false },
    { name: 'Procedure Note', desc: 'Document procedures and interventions', favorite: false },
    { name: 'Telehealth Visit', desc: 'Remote consultation documentation', favorite: false },
    { name: 'Discharge Summary', desc: 'Patient discharge documentation', favorite: false },
  ];

  // Features for demo panel
  const features = [
    { icon: '🎙️', name: 'Real-Time Transcription', desc: 'Transcribes in 110+ languages with speaker diarization' },
    { icon: '💬', name: 'Ask Heidi', desc: 'AI command bar to modify notes and generate documents' },
    { icon: '✨', name: 'Magic Paste', desc: 'One-click transfer to any EHR system' },
    { icon: '💰', name: 'Auto Billing Codes', desc: 'ICD-10, SNOMED-CT, HCPCS suggestions' },
    { icon: '📋', name: 'Template Library', desc: 'Thousands of customizable templates' },
    { icon: '📄', name: 'Document Generation', desc: 'Referrals, summaries, certificates' },
    { icon: '🧠', name: 'Brain Modes', desc: 'Standard (precise) vs Extended (detailed)' },
    { icon: '🔗', name: 'EHR Integrations', desc: 'Epic, Athena, Best Practice & more' },
  ];

  return (
    <div className="flex h-screen bg-gray-100 font-sans text-sm">
      {/* Notification */}
      {notification && (
        <div className="fixed top-4 left-1/2 transform -translate-x-1/2 bg-gray-800 text-white px-6 py-3 rounded-lg shadow-lg z-50 animate-pulse">
          {notification}
        </div>
      )}

      {/* Left Sidebar */}
      <aside className="w-64 bg-slate-900 text-white flex flex-col">
        <div className="p-4 border-b border-slate-700">
          <div className="flex items-center gap-3">
            <div className="w-10 h-10 bg-gradient-to-br from-emerald-400 to-emerald-600 rounded-xl flex items-center justify-center text-xl font-bold">
              H
            </div>
            <span className="text-xl font-bold">Heidi</span>
          </div>
          <button
            onClick={() => {
              setRecordingTime(0);
              setTranscript([]);
              setGeneratedNote(null);
              showNotif('🆕 New session started');
            }}
            className="w-full mt-4 py-2.5 bg-emerald-500 hover:bg-emerald-600 rounded-lg font-semibold flex items-center justify-center gap-2 transition-colors"
          >
            + New Session
          </button>
        </div>

        <nav className="flex-1 p-3 overflow-y-auto">
          <div className="text-xs text-slate-400 uppercase tracking-wider px-3 py-2">Menu</div>
          {['Sessions', 'Templates', 'Integrations', 'Settings'].map(item => (
            <button
              key={item}
              onClick={() => item === 'Templates' && setShowTemplateModal(true)}
              className={`w-full text-left px-3 py-2.5 rounded-lg mb-1 flex items-center gap-3 transition-colors ${
                item === 'Sessions' ? 'bg-emerald-500/20 text-emerald-400' : 'hover:bg-slate-800'
              }`}
            >
              <span>{item === 'Sessions' ? '📝' : item === 'Templates' ? '📋' : item === 'Integrations' ? '🔗' : '⚙️'}</span>
              {item}
            </button>
          ))}

          <div className="text-xs text-slate-400 uppercase tracking-wider px-3 py-2 mt-4">Recent Sessions</div>
          {recentSessions.map(session => (
            <div
              key={session.id}
              className={`px-3 py-2.5 rounded-lg mb-1 cursor-pointer transition-colors ${
                session.active ? 'bg-emerald-500/15 border-l-2 border-emerald-400' : 'hover:bg-slate-800'
              }`}
            >
              <div className="text-sm truncate">{session.name}</div>
              <div className="text-xs text-slate-500">{session.date}</div>
            </div>
          ))}
        </nav>
      </aside>

      {/* Main Content */}
      <main className="flex-1 flex flex-col overflow-hidden">
        {/* Top Bar */}
        <header className="bg-white px-6 py-3 border-b flex items-center justify-between">
          <input
            type="text"
            defaultValue="John Smith - Follow Up Visit"
            className="text-lg font-semibold bg-transparent border-none outline-none w-80"
          />
          <div className="flex items-center gap-3">
            <span className="px-3 py-1 bg-green-100 text-green-700 rounded-full text-xs flex items-center gap-1">
              🔗 Epic Connected
            </span>
            <div className="flex bg-gray-100 rounded-full p-1">
              <button
                onClick={() => { setBrainMode('standard'); showNotif('🧠 Standard Scribe: Fast, precise'); }}
                className={`px-4 py-1.5 rounded-full text-xs font-medium transition-all ${
                  brainMode === 'standard' ? 'bg-white shadow text-blue-600' : 'text-gray-600'
                }`}
              >
                🧠 Standard
              </button>
              <button
                onClick={() => { setBrainMode('extended'); showNotif('🧠 Extended Scribe: Detailed, thorough'); }}
                className={`px-4 py-1.5 rounded-full text-xs font-medium transition-all ${
                  brainMode === 'extended' ? 'bg-white shadow text-orange-600' : 'text-gray-600'
                }`}
              >
                🧠 Extended
              </button>
            </div>
            <button
              onClick={() => setShowTemplateModal(true)}
              className="px-3 py-1.5 border rounded-lg hover:bg-gray-50 flex items-center gap-1"
            >
              📋 Templates
            </button>
          </div>
        </header>

        {/* Content Grid */}
        <div className="flex-1 grid grid-cols-2 gap-4 p-4 overflow-hidden">
          {/* Transcription Panel */}
          <div className="bg-white rounded-xl shadow-sm flex flex-col overflow-hidden">
            <div className="px-4 py-3 border-b flex items-center justify-between">
              <div className="font-semibold flex items-center gap-2">🎙️ Transcription</div>
              <div className="flex items-center gap-3">
                {/* Audio Bars */}
                <div className="flex items-center gap-1 h-6">
                  {[1,2,3].map(i => (
                    <div
                      key={i}
                      className={`w-1 bg-emerald-500 rounded transition-all ${isRecording ? 'animate-pulse' : ''}`}
                      style={{ height: isRecording ? `${Math.random() * 20 + 8}px` : '8px' }}
                    />
                  ))}
                </div>
                <span className="font-mono text-base">{formatTime(recordingTime)}</span>
                <button
                  onClick={toggleRecording}
                  className={`w-12 h-12 rounded-full flex items-center justify-center transition-all ${
                    isRecording
                      ? 'bg-red-500 animate-pulse shadow-lg shadow-red-500/30'
                      : 'bg-red-500 hover:bg-red-600'
                  }`}
                >
                  <div className={`bg-white ${isRecording ? 'w-4 h-4 rounded' : 'w-4 h-4 rounded-full'}`} />
                </button>
              </div>
            </div>

            <div className="flex-1 p-4 overflow-y-auto">
              {transcript.length === 0 ? (
                <div className="h-full flex flex-col items-center justify-center text-gray-400">
                  <span className="text-4xl mb-3">🎙️</span>
                  <span>Click record to start transcribing</span>
                </div>
              ) : (
                <div className="space-y-3">
                  {transcript.map((entry, i) => (
                    <div key={i}>
                      <div className="font-semibold text-emerald-600">{entry.speaker}:</div>
                      <div className="text-gray-600 leading-relaxed">{entry.text}</div>
                    </div>
                  ))}
                  {isRecording && (
                    <div className="flex gap-1 px-3 py-2 bg-gray-100 rounded-full w-fit">
                      {[1,2,3].map(i => (
                        <div key={i} className="w-2 h-2 bg-gray-400 rounded-full animate-bounce" style={{ animationDelay: `${i * 0.15}s` }} />
                      ))}
                    </div>
                  )}
                </div>
              )}
            </div>

            {/* Context Section */}
            <div className="p-4 border-t bg-gray-50">
              <div className="text-xs font-semibold mb-2 flex items-center gap-1">📌 Context (Silent Notes)</div>
              <textarea
                placeholder="Add info not captured in audio (e.g., vitals, observations)..."
                className="w-full h-20 p-3 border border-dashed border-gray-300 rounded-lg text-sm resize-none focus:border-emerald-500 outline-none"
              />
            </div>
          </div>

          {/* Clinical Note Panel */}
          <div className="bg-white rounded-xl shadow-sm flex flex-col overflow-hidden">
            <div className="px-4 py-3 border-b flex items-center justify-between">
              <div className="font-semibold flex items-center gap-2">
                📄 Clinical Note
                <span className="text-xs text-gray-400 font-normal">{selectedTemplate}</span>
              </div>
              <div className="flex gap-2">
                <button
                  onClick={() => showNotif('📋 Note copied to clipboard!')}
                  className="px-3 py-1.5 border rounded-lg hover:bg-gray-50 text-xs"
                >
                  📋 Copy
                </button>
                <button
                  onClick={() => showNotif('✨ Magic Paste - Note transferred to EHR!')}
                  className="px-3 py-1.5 bg-emerald-500 text-white rounded-lg hover:bg-emerald-600 text-xs"
                >
                  ✨ Magic Paste
                </button>
              </div>
            </div>

            <div className="flex-1 p-4 overflow-y-auto">
              {!generatedNote ? (
                <div className="h-full flex flex-col items-center justify-center text-gray-400">
                  <span className="text-4xl mb-3">📝</span>
                  <span>Clinical note will appear after transcription</span>
                </div>
              ) : (
                <div className="space-y-4">
                  {['subjective', 'objective', 'assessment', 'plan'].map(section => (
                    <div key={section}>
                      <div className="text-xs font-bold uppercase text-gray-700 mb-1">{section}</div>
                      <div className="text-gray-600 whitespace-pre-line text-sm leading-relaxed">
                        {generatedNote[section]}
                      </div>
                    </div>
                  ))}

                  {/* Billing Codes */}
                  <div className="mt-4 p-4 bg-gray-50 rounded-lg">
                    <div className="flex items-center justify-between mb-3">
                      <span className="font-semibold text-sm flex items-center gap-2">💰 Suggested Billing Codes</span>
                      <div className="flex gap-1">
                        {billingCodes.map((_, i) => (
                          <div key={i} className={`w-2 h-2 rounded-full ${billingCodes[i].confirmed ? 'bg-emerald-500' : 'bg-gray-300'}`} />
                        ))}
                      </div>
                    </div>
                    {billingCodes.map((code, i) => (
                      <div
                        key={i}
                        onClick={() => confirmCode(i)}
                        className="flex items-center justify-between p-3 bg-white rounded-lg mb-2 border cursor-pointer hover:border-emerald-500 transition-colors"
                      >
                        <div className="flex items-center gap-3">
                          <span className="text-xs font-semibold px-2 py-0.5 bg-blue-100 text-blue-700 rounded">{code.type}</span>
                          <span className="font-mono font-medium">{code.code}</span>
                          <span className="text-gray-500 text-xs">{code.desc}</span>
                        </div>
                        <div className={`w-5 h-5 rounded-full border-2 flex items-center justify-center text-white text-xs ${
                          code.confirmed ? 'bg-emerald-500 border-emerald-500' : 'border-gray-300'
                        }`}>
                          {code.confirmed && '✓'}
                        </div>
                      </div>
                    ))}
                  </div>
                </div>
              )}
            </div>

            {/* Ask Heidi Bar */}
            <div className="p-4 border-t bg-gray-50 flex gap-2">
              <input
                type="text"
                value={askHeidiInput}
                onChange={(e) => setAskHeidiInput(e.target.value)}
                onKeyDown={(e) => e.key === 'Enter' && handleAskHeidi()}
                placeholder="Ask Heidi to modify the note..."
                className="flex-1 px-4 py-2.5 border rounded-full outline-none focus:border-emerald-500"
              />
              <button
                onClick={handleAskHeidi}
                className="w-10 h-10 bg-emerald-500 hover:bg-emerald-600 rounded-full flex items-center justify-center text-white"
              >
                →
              </button>
            </div>
          </div>
        </div>

        {/* Bottom Bar */}
        <div className="bg-white px-4 py-3 border-t flex justify-between">
          <div className="flex gap-2">
            {['Referral', 'Patient Summary', 'Medical Certificate'].map(doc => (
              <button
                key={doc}
                onClick={() => showNotif(`📄 Generating ${doc}...`)}
                className="px-3 py-1.5 border rounded-lg hover:bg-gray-50 text-xs"
              >
                📄 {doc}
              </button>
            ))}
          </div>
          <div className="flex gap-2">
            <button
              onClick={() => showNotif('🔄 Pushing note to Epic EHR...')}
              className="px-3 py-1.5 border rounded-lg hover:bg-gray-50 text-xs"
            >
              🔄 Push to EHR
            </button>
            <button
              onClick={() => showNotif('✅ Session approved and completed!')}
              className="px-4 py-1.5 bg-emerald-500 text-white rounded-lg hover:bg-emerald-600 text-xs"
            >
              ✅ Approve & Complete
            </button>
          </div>
        </div>
      </main>

      {/* Template Modal */}
      {showTemplateModal && (
        <div className="fixed inset-0 bg-black/50 flex items-center justify-center z-40" onClick={() => setShowTemplateModal(false)}>
          <div className="bg-white rounded-2xl w-[500px] max-h-[80vh] overflow-hidden" onClick={e => e.stopPropagation()}>
            <div className="px-6 py-4 border-b flex justify-between items-center">
              <span className="font-semibold text-lg">Select Template</span>
              <button onClick={() => setShowTemplateModal(false)} className="w-8 h-8 rounded-lg bg-gray-100 hover:bg-gray-200">×</button>
            </div>
            <div className="p-6">
              <input placeholder="Search templates..." className="w-full px-4 py-2.5 border rounded-lg mb-4 outline-none focus:border-emerald-500" />
              <div className="flex gap-2 mb-4">
                {['My Templates', 'Community', 'Specialty'].map(tab => (
                  <button key={tab} className={`px-4 py-1.5 rounded-full text-sm ${tab === 'My Templates' ? 'bg-emerald-500 text-white' : 'bg-gray-100'}`}>
                    {tab}
                  </button>
                ))}
              </div>
              <div className="grid grid-cols-2 gap-3">
                {templates.map(t => (
                  <div
                    key={t.name}
                    onClick={() => { setSelectedTemplate(t.name); setShowTemplateModal(false); showNotif(`📋 Template: ${t.name}`); }}
                    className={`p-4 border rounded-xl cursor-pointer hover:border-emerald-500 transition-colors ${
                      selectedTemplate === t.name ? 'border-emerald-500 bg-emerald-50' : ''
                    }`}
                  >
                    <div className="font-semibold">{t.name} {t.favorite && <span className="text-yellow-500">★</span>}</div>
                    <div className="text-xs text-gray-500 mt-1">{t.desc}</div>
                  </div>
                ))}
              </div>
            </div>
          </div>
        </div>
      )}

      {/* Feature Demo Panel */}
      <div className={`fixed right-0 top-0 h-full w-80 bg-slate-900 text-white p-5 overflow-y-auto transition-transform z-30 ${showFeatureDemo ? 'translate-x-0' : 'translate-x-full'}`}>
        <div className="flex justify-between items-center mb-6">
          <span className="font-semibold">Feature Simulation</span>
          <button onClick={() => setShowFeatureDemo(false)} className="w-8 h-8 rounded-lg bg-white/10 hover:bg-white/20">×</button>
        </div>
        {features.map(f => (
          <div
            key={f.name}
            onClick={() => { setShowFeatureDemo(false); showNotif(`Demo: ${f.name}`); }}
            className="p-4 bg-white/5 rounded-xl mb-3 cursor-pointer hover:bg-white/10 border border-transparent hover:border-emerald-500 transition-all"
          >
            <div className="font-semibold flex items-center gap-2">{f.icon} {f.name}</div>
            <div className="text-xs text-gray-400 mt-1">{f.desc}</div>
            <button className="mt-2 px-3 py-1 bg-emerald-500 rounded text-xs">Try Demo →</button>
          </div>
        ))}
      </div>

      {/* Feature Demo Toggle */}
      <button
        onClick={() => setShowFeatureDemo(true)}
        className="fixed right-5 bottom-5 px-5 py-3 bg-emerald-500 text-white rounded-full shadow-lg hover:bg-emerald-600 flex items-center gap-2 font-semibold z-20"
      >
        🎮 Feature Demo
      </button>
    </div>
  );
}
```

---

## Feature Simulation Guide

### How to Use Each Feature in the Prototype

| Feature | How to Trigger | What Happens |
|---------|----------------|--------------|
| **Start Recording** | Click red record button | Timer starts, audio bars animate, typing indicator appears |
| **Stop Recording** | Click square stop button | Note auto-generates with SOAP format |
| **New Session** | Click "+ New Session" in sidebar | Clears transcript and note |
| **Change Template** | Click "Templates" button | Opens modal with template options |
| **Brain Mode Toggle** | Click Standard/Extended toggle | Shows notification about mode change |
| **Copy Note** | Click "Copy" button | Shows "copied to clipboard" notification |
| **Magic Paste** | Click "Magic Paste" button | Simulates EHR transfer |
| **Ask Heidi** | Type command and press Enter | Shows processing notification |
| **Confirm Billing Code** | Click any billing code row | Toggles confirmation checkmark |
| **Generate Document** | Click Referral/Summary/Certificate | Shows generating notification |
| **Push to EHR** | Click "Push to EHR" | Simulates Epic integration |
| **Feature Demo Panel** | Click "🎮 Feature Demo" button | Opens feature showcase panel |

---

## Customization Options

### To Modify the Prototype:

1. **Change Colors:** Update the Tailwind classes (emerald-500, slate-900, etc.)
2. **Add Templates:** Extend the `templates` array
3. **Modify Note Format:** Update the `generateNote` function
4. **Add Billing Codes:** Extend the `billingCodes` initial state
5. **Change Sessions:** Update the `recentSessions` array

### To Add New Features:

1. **New Document Types:** Add to the bottom bar buttons
2. **New Integrations:** Update the integration badge and add new EHR options
3. **Voice Commands:** Extend the Ask Heidi command processing
4. **New Brain Modes:** Add additional modes to the toggle

---

## Product Features Included in Prototype

### Core Features
- [x] Real-time transcription simulation
- [x] Speaker diarization (Doctor/Patient)
- [x] Recording controls with timer
- [x] Audio waveform visualization
- [x] SOAP note auto-generation
- [x] Context/silent notes section
- [x] Template selection modal
- [x] Ask Heidi AI command bar
- [x] Magic Paste button
- [x] Push to EHR functionality
- [x] Billing code suggestions (ICD-10, CPT)
- [x] Code confirmation interface
- [x] Document generation (Referral, Summary, Certificate)
- [x] Brain mode toggle (Standard/Extended)
- [x] Session management sidebar
- [x] EHR integration badge
- [x] Feature demo panel

### UI Elements
- [x] Three-panel layout
- [x] Dark sidebar navigation
- [x] Top action bar
- [x] Bottom action bar
- [x] Modal overlays
- [x] Notification system
- [x] Animated elements
- [x] Responsive grid

---

## Sources & References

This prototype is based on research from:
- [Heidi Health Official Website](https://www.heidihealth.com)
- [Heidi Health Changelog](https://www.heidihealth.com/en-us/changelog)
- [Heidi Chrome Extension](https://chromewebstore.google.com/detail/heidi/iacmkikjodakeidnjgibjajdfippnmhh)
- [Heidi User Handbook](https://intercom.help/heidi-health-handbook/en/articles/10493537-heidi-user-handbook)
- [Heidi Support Articles](https://www.heidihealth.com/support)

---

*Copy the React code above into a Claude Artifact with React+Tailwind support to create an interactive prototype of the Heidi Health interface.*
