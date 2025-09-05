import { useState } from 'react';
import { Send, Copy, Check, Mail, Sparkles, MessageSquare, User, Globe, Heart } from 'lucide-react';

const TRANSLATIONS = {
  "en-US": {
    "emailWritingAssistant": "E-mail Writing Assistant",
    "transformThoughtsDescription": "Gode og korrekte e-mails i en fart",
    "yourThoughts": "Your Thoughts",
    "thoughtsPlaceholder": "Write what you want to communicate in Danish or English... Don't worry about grammar or structure - just get your ideas down.",
    "tipKeyboardShortcut": "💡 Tip: Press Cmd/Ctrl + Enter to generate your email",
    "emailTone": "E-mail Tone",
    "professionalTone": "Professional",
    "professionalDescription": "Clear and business-appropriate",
    "warmTone": "Warm",
    "warmDescription": "Friendly and approachable",
    "conciseTone": "Concise",
    "conciseDescription": "Brief and to the point",
    "formalTone": "Formal",
    "formalDescription": "Traditional and respectful",
    "casualTone": "Casual",
    "casualDescription": "Relaxed and conversational",
    "persuasiveTone": "Persuasive",
    "persuasiveDescription": "Compelling and convincing",
    "outputLanguage": "Output Language",
    "english": "English",
    "danish": "Danish",
    "politenessLevel": "Politeness Level",
    "veryPolite": "Very Polite",
    "balanced": "Balanced",
    "direct": "Direct",
    "contextOptional": "Context (Optional)",
    "hide": "Hide",
    "show": "Show",
    "contextDescription": "Paste the email you're responding to for better context",
    "contextPlaceholder": "Paste the original email here...",
    "craftingEmail": "Crafting your email...",
    "generateEmail": "Generer e-mail",
    "generatedEmail": "Genereret e-mail",
    "copied": "Copied!",
    "copy": "Copy",
    "emailWillAppearHere": "Your polished email will appear here",
    "getStartedPrompt": "Enter your thoughts and select your preferences to get started",
    "proTips": "✨ Pro Tips",
    "tipBeSpecific": "• Be specific about what you want to achieve",
    "tipIncludeDetails": "• Include key details even if roughly written",
    "tipTryTones": "• Try different tones to see what works best",
    "tipAddContext": "• Add context for more personalized responses",
    "tipLanguage": "• Write your thoughts in whichever language feels natural"
  },
  "da-DK": {
    "emailWritingAssistant": "E-mail Skriveassistent",
    "transformThoughtsDescription": "Gode og korrekte e-mails i en fart",
    "yourThoughts": "Dine Tanker",
    "thoughtsPlaceholder": "Skriv hvad du vil kommunikere på dansk eller engelsk... Bare få dine idéer ned - bekymr dig ikke om grammatik eller struktur.",
    "tipKeyboardShortcut": "💡 Tip: Tryk Cmd/Ctrl + Enter for at generere din e-mail",
    "emailTone": "E-mail Tone",
    "professionalTone": "Professionel",
    "professionalDescription": "Klar og forretningspassende",
    "warmTone": "Varm",
    "warmDescription": "Venlig og imødekommende",
    "conciseTone": "Koncis",
    "conciseDescription": "Kort og præcis",
    "formalTone": "Formel",
    "formalDescription": "Traditionel og respektfuld",
    "casualTone": "Afslappet",
    "casualDescription": "Afslappet og samtaleagtig",
    "persuasiveTone": "Overbevisende",
    "persuasiveDescription": "Medrivende og overbevisende",
    "outputLanguage": "Output Sprog",
    "english": "Engelsk",
    "danish": "Dansk",
    "politenessLevel": "Høflighedsniveau",
    "veryPolite": "Meget Høflig",
    "balanced": "Balanceret",
    "direct": "Direkte",
    "contextOptional": "Kontekst (Valgfrit)",
    "hide": "Skjul",
    "show": "Vis",
    "contextDescription": "Indsæt den e-mail du svarer på for bedre kontekst",
    "contextPlaceholder": "Indsæt den oprindelige e-mail her...",
    "craftingEmail": "Skriver din e-mail...",
    "generateEmail": "Generer e-mail",
    "generatedEmail": "Genereret e-mail",
    "copied": "Kopieret!",
    "copy": "Kopier",
    "emailWillAppearHere": "Din polerede e-mail vil vises her",
    "getStartedPrompt": "Indtast dine tanker og vælg dine præferencer for at komme i gang",
    "proTips": "✨ Pro Tips",
    "tipBeSpecific": "• Vær specifik om hvad du vil opnå",
    "tipIncludeDetails": "• Inkluder vigtige detaljer selv hvis de er skrevet groft",
    "tipTryTones": "• Prøv forskellige toner for at se hvad der virker bedst",
    "tipAddContext": "• Tilføj kontekst for mere personlige svar",
    "tipLanguage": "• Skriv dine tanker på det sprog der føles naturligt"
  }
};

const appLocale = '{{APP_LOCALE}}';
const browserLocale = navigator.languages?.[0] || navigator.language || 'en-US';
const findMatchingLocale = (locale) => {
  if (TRANSLATIONS[locale]) return locale;
  const lang = locale.split('-')[0];
  const match = Object.keys(TRANSLATIONS).find(key => key.startsWith(lang + '-'));
  return match || 'en-US';
};
const locale = (appLocale !== '{{APP_LOCALE}}') ? findMatchingLocale(appLocale) : findMatchingLocale(browserLocale);
const t = (key) => TRANSLATIONS[locale]?.[key] || TRANSLATIONS['en-US'][key] || key;

export default function EmailWriterApp() {
  const [rawThoughts, setRawThoughts] = useState('');
  const [tone, setTone] = useState('concise');
  const [outputLanguage, setOutputLanguage] = useState('english');
  const [politenessLevel, setPolitenessLevel] = useState(1); // 0 = direct, 1 = balanced, 2 = very polite
  const [contextEmail, setContextEmail] = useState('');
  const [generatedEmail, setGeneratedEmail] = useState('');
  const [isLoading, setIsLoading] = useState(false);
  const [copied, setCopied] = useState(false);
  const [showContext, setShowContext] = useState(false);

  const tones = [
    { value: 'professional', label: t('professionalTone'), description: t('professionalDescription') },
    { value: 'warm', label: t('warmTone'), description: t('warmDescription') },
    { value: 'concise', label: t('conciseTone'), description: t('conciseDescription') },
    { value: 'formal', label: t('formalTone'), description: t('formalDescription') },
    { value: 'casual', label: t('casualTone'), description: t('casualDescription') },
    { value: 'persuasive', label: t('persuasiveTone'), description: t('persuasiveDescription') }
  ];

  const politenessLabels = [t('direct'), t('balanced'), t('veryPolite')];
  const politenessDescriptions = [
    'Straight to the point, efficient',
    'Professional with some courtesy',
    'Extra courteous, very respectful'
  ];

  const generateEmail = async () => {
    if (!rawThoughts.trim()) return;

    setIsLoading(true);
    try {
      const contextPart = contextEmail.trim() 
        ? `\n\nContext - I am responding to this email:\n"${contextEmail}"\n\n`
        : '';

      const politenessGuidance = [
        'Be direct and efficient. Use minimal pleasantries. Get straight to the point.',
        'Use professional courtesy. Include appropriate pleasantries but keep them balanced.',
        'Be very polite and courteous. Include thoughtful pleasantries, express gratitude, and use respectful language throughout.'
      ];

      const targetLanguage = outputLanguage === 'english' ? 'English' : 'Danish';
      
      const prompt = `You are an expert email writer. Transform the following raw thoughts into a well-crafted email.

Raw thoughts: "${rawThoughts}"${contextPart}

Instructions:
- Write the email in ${targetLanguage}
- Use a ${tone} tone throughout
- Politeness level: ${politenessGuidance[politenessLevel]}
- Make it clear, engaging, and well-structured
- Ensure proper email etiquette for ${targetLanguage} business communication
- Do not include a subject line
- Write a complete email body only

Respond with ONLY the email body content. Do not include any explanations or additional text outside of the email.`;

      const response = await fetch("https://api.anthropic.com/v1/messages", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          model: "claude-sonnet-4-20250514",
          max_tokens: 1500,
          messages: [{ role: "user", content: prompt }]
        })
      });

      if (!response.ok) {
        throw new Error(`API request failed: ${response.status}`);
      }

      const data = await response.json();
      setGeneratedEmail(data.content[0].text.trim());
    } catch (error) {
      console.error('Error generating email:', error);
      setGeneratedEmail('Sorry, there was an error generating your email. Please try again.');
    } finally {
      setIsLoading(false);
    }
  };

  const copyToClipboard = async () => {
    try {
      await navigator.clipboard.writeText(generatedEmail);
      setCopied(true);
      setTimeout(() => setCopied(false), 2000);
    } catch (error) {
      console.error('Failed to copy:', error);
    }
  };

  const handleKeyPress = (e) => {
    if (e.key === 'Enter' && (e.metaKey || e.ctrlKey)) {
      generateEmail();
    }
  };

  return (
    <div className="min-h-screen" style={{ backgroundColor: '#16a34a' }}>
      {/* Header */}
      <div className="relative overflow-hidden">
        <div className="relative max-w-6xl mx-auto px-6 py-12">
          <div className="text-center">
            <div className="inline-flex items-center justify-center w-16 h-16 rounded-2xl mb-6 shadow-lg" style={{ background: 'linear-gradient(135deg, #f97316, #ea580c)' }}>
              <Mail className="w-8 h-8" style={{ color: '#ffffff' }} />
            </div>
            <h1 className="text-4xl font-bold mb-4" style={{ color: '#fdba74' }}>
              {t('emailWritingAssistant')}
            </h1>
            <p className="text-xl max-w-2xl mx-auto" style={{ color: '#dcfce7' }}>
              {t('transformThoughtsDescription')}
            </p>
          </div>
        </div>
      </div>

      {/* Main Content */}
      <div className="max-w-6xl mx-auto px-6 pb-12">
        <div className="grid lg:grid-cols-2 gap-8">
          
          {/* Input Section */}
          <div className="space-y-6">
            <div className="backdrop-blur-sm rounded-2xl p-8 shadow-xl border" style={{ backgroundColor: '#15803d', borderColor: '#16a34a' }}>
              <div className="flex items-center gap-3 mb-6">
                <div className="w-10 h-10 rounded-xl flex items-center justify-center" style={{ backgroundColor: '#166534' }}>
                  <MessageSquare className="w-5 h-5" style={{ color: '#fdba74' }} />
                </div>
                <h2 className="text-2xl font-semibold" style={{ color: '#ffffff' }}>{t('yourThoughts')}</h2>
              </div>
              
              <textarea
                value={rawThoughts}
                onChange={(e) => setRawThoughts(e.target.value)}
                onKeyDown={handleKeyPress}
                placeholder={t('thoughtsPlaceholder')}
                className="w-full h-40 p-4 border-2 rounded-xl resize-none focus:ring-2 focus:ring-orange-500 focus:border-transparent transition-all duration-200 backdrop-blur-sm"
                style={{ 
                  backgroundColor: '#166534',
                  borderColor: '#16a34a',
                  color: '#ffffff'
                }}
              />
              
              <div className="mt-4 text-sm" style={{ color: '#bbf7d0' }}>
                {t('tipKeyboardShortcut')}
              </div>
            </div>

            {/* Language Selection */}
            <div className="backdrop-blur-sm rounded-2xl p-8 shadow-xl border" style={{ backgroundColor: '#15803d', borderColor: '#16a34a' }}>
              <div className="flex items-center gap-3 mb-6">
                <div className="w-10 h-10 rounded-xl flex items-center justify-center" style={{ backgroundColor: '#166534' }}>
                  <Globe className="w-5 h-5" style={{ color: '#fdba74' }} />
                </div>
                <h2 className="text-2xl font-semibold" style={{ color: '#ffffff' }}>{t('outputLanguage')}</h2>
              </div>
              
              <div className="grid grid-cols-2 gap-4">
                <button
                  onClick={() => setOutputLanguage('english')}
                  className={`p-4 rounded-xl border-2 transition-all duration-200 text-center ${
                    outputLanguage === 'english' ? 'shadow-md' : 'hover:opacity-80'
                  }`}
                  style={{
                    backgroundColor: outputLanguage === 'english' ? '#f97316' : '#166534',
                    borderColor: outputLanguage === 'english' ? '#ea580c' : '#16a34a',
                    color: '#ffffff'
                  }}
                >
                  <div className="font-medium flex items-center justify-center gap-2">
                    🇺🇸 {t('english')}
                  </div>
                </button>
                <button
                  onClick={() => setOutputLanguage('danish')}
                  className={`p-4 rounded-xl border-2 transition-all duration-200 text-center ${
                    outputLanguage === 'danish' ? 'shadow-md' : 'hover:opacity-80'
                  }`}
                  style={{
                    backgroundColor: outputLanguage === 'danish' ? '#f97316' : '#166534',
                    borderColor: outputLanguage === 'danish' ? '#ea580c' : '#16a34a',
                    color: '#ffffff'
                  }}
                >
                  <div className="font-medium flex items-center justify-center gap-2">
                    🇩🇰 {t('danish')}
                  </div>
                </button>
              </div>
            </div>

            {/* Tone Selection */}
            <div className="backdrop-blur-sm rounded-2xl p-8 shadow-xl border" style={{ backgroundColor: '#15803d', borderColor: '#16a34a' }}>
              <div className="flex items-center gap-3 mb-6">
                <div className="w-10 h-10 rounded-xl flex items-center justify-center" style={{ backgroundColor: '#166534' }}>
                  <Sparkles className="w-5 h-5" style={{ color: '#fdba74' }} />
                </div>
                <h2 className="text-2xl font-semibold" style={{ color: '#ffffff' }}>{t('emailTone')}</h2>
              </div>
              
              <div className="grid grid-cols-2 gap-3">
                {tones.map((toneOption) => (
                  <button
                    key={toneOption.value}
                    onClick={() => setTone(toneOption.value)}
                    className={`p-4 rounded-xl border-2 transition-all duration-200 text-left ${
                      tone === toneOption.value ? 'shadow-md' : 'hover:opacity-80'
                    }`}
                    style={{
                      backgroundColor: tone === toneOption.value ? '#f97316' : '#166534',
                      borderColor: tone === toneOption.value ? '#ea580c' : '#16a34a'
                    }}
                  >
                    <div className="font-medium" style={{ color: '#ffffff' }}>{toneOption.label}</div>
                    <div className="text-sm mt-1" style={{ color: tone === toneOption.value ? '#dcfce7' : '#bbf7d0' }}>{toneOption.description}</div>
                  </button>
                ))}
              </div>
            </div>

            {/* Politeness Level */}
            <div className="backdrop-blur-sm rounded-2xl p-8 shadow-xl border" style={{ backgroundColor: '#15803d', borderColor: '#16a34a' }}>
              <div className="flex items-center gap-3 mb-6">
                <div className="w-10 h-10 rounded-xl flex items-center justify-center" style={{ backgroundColor: '#166534' }}>
                  <Heart className="w-5 h-5" style={{ color: '#fdba74' }} />
                </div>
                <h2 className="text-2xl font-semibold" style={{ color: '#ffffff' }}>{t('politenessLevel')}</h2>
              </div>
              
              <div className="space-y-4">
                <div className="flex items-center justify-between text-sm" style={{ color: '#bbf7d0' }}>
                  <span>{t('direct')}</span>
                  <span>{t('veryPolite')}</span>
                </div>
                
                <div className="relative">
                  <input
                    type="range"
                    min="0"
                    max="2"
                    step="1"
                    value={politenessLevel}
                    onChange={(e) => setPolitenessLevel(parseInt(e.target.value))}
                    className="w-full h-2 rounded-lg appearance-none cursor-pointer slider"
                    style={{ backgroundColor: '#166534' }}
                  />
                  <div className="flex justify-between text-xs mt-2" style={{ color: '#bbf7d0' }}>
                    <span>Direct</span>
                    <span>Balanced</span>
                    <span>Very Polite</span>
                  </div>
                </div>
                
                <div className="rounded-lg p-3 border" style={{ backgroundColor: '#166534', borderColor: '#16a34a' }}>
                  <div className="font-medium" style={{ color: '#ffffff' }}>{politenessLabels[politenessLevel]}</div>
                  <div className="text-sm" style={{ color: '#bbf7d0' }}>{politenessDescriptions[politenessLevel]}</div>
                </div>
              </div>
            </div>

            {/* Context Email Section */}
            <div className="backdrop-blur-sm rounded-2xl p-8 shadow-xl border" style={{ backgroundColor: '#15803d', borderColor: '#16a34a' }}>
              <div className="flex items-center justify-between mb-6">
                <div className="flex items-center gap-3">
                  <div className="w-10 h-10 rounded-xl flex items-center justify-center" style={{ backgroundColor: '#166534' }}>
                    <User className="w-5 h-5" style={{ color: '#fdba74' }} />
                  </div>
                  <h2 className="text-2xl font-semibold" style={{ color: '#ffffff' }}>{t('contextOptional')}</h2>
                </div>
                <button
                  onClick={() => setShowContext(!showContext)}
                  className="font-medium transition-colors hover:opacity-80"
                  style={{ color: '#fdba74' }}
                >
                  {showContext ? t('hide') : t('show')}
                </button>
              </div>
              
              {showContext && (
                <>
                  <p className="mb-4" style={{ color: '#bbf7d0' }}>
                    {t('contextDescription')}
                  </p>
                  <textarea
                    value={contextEmail}
                    onChange={(e) => setContextEmail(e.target.value)}
                    placeholder={t('contextPlaceholder')}
                    className="w-full h-32 p-4 border-2 rounded-xl resize-none focus:ring-2 focus:ring-orange-500 focus:border-transparent transition-all duration-200 backdrop-blur-sm"
                    style={{ 
                      backgroundColor: '#166534',
                      borderColor: '#16a34a',
                      color: '#ffffff'
                    }}
                  />
                </>
              )}
            </div>

            {/* Generate Button */}
            <button
              onClick={generateEmail}
              disabled={isLoading || !rawThoughts.trim()}
              className="w-full py-4 px-8 rounded-xl font-semibold text-lg shadow-lg hover:shadow-xl transform hover:scale-[1.02] transition-all duration-200 disabled:opacity-50 disabled:cursor-not-allowed disabled:transform-none flex items-center justify-center gap-3"
              style={{ 
                background: isLoading || !rawThoughts.trim() ? '#f97316' : 'linear-gradient(135deg, #f97316, #ea580c)',
                color: '#ffffff'
              }}
            >
              {isLoading ? (
                <>
                  <div className="w-5 h-5 border-2 border-white/30 border-t-white rounded-full animate-spin"></div>
                  <span style={{ color: '#ffffff' }}>Skriver din e-mail...</span>
                </>
              ) : (
                <>
                  <Send className="w-5 h-5" style={{ color: '#ffffff' }} />
                  <span style={{ color: '#ffffff' }}>Generer e-mail</span>
                </>
              )}
            </button>
          </div>

          {/* Output Section */}
          <div className="space-y-6">
            <div className="backdrop-blur-sm rounded-2xl p-8 shadow-xl border min-h-96" style={{ backgroundColor: '#15803d', borderColor: '#16a34a' }}>
              <div className="flex items-center justify-between mb-6">
                <div className="flex items-center gap-3">
                  <div className="w-10 h-10 rounded-xl flex items-center justify-center" style={{ backgroundColor: '#166534' }}>
                    <Mail className="w-5 h-5" style={{ color: '#fdba74' }} />
                  </div>
                  <h2 className="text-2xl font-semibold" style={{ color: '#ffffff' }}>{t('generatedEmail')}</h2>
                </div>
                
                {generatedEmail && (
                  <button
                    onClick={copyToClipboard}
                    className="flex items-center gap-2 px-4 py-2 rounded-lg transition-colors font-medium border hover:opacity-80"
                    style={{ 
                      backgroundColor: '#f97316',
                      borderColor: '#ea580c',
                      color: '#ffffff'
                    }}
                  >
                    {copied ? (
                      <>
                        <Check className="w-4 h-4" />
                        {t('copied')}
                      </>
                    ) : (
                      <>
                        <Copy className="w-4 h-4" />
                        {t('copy')}
                      </>
                    )}
                  </button>
                )}
              </div>
              
              {generatedEmail ? (
                <div className="rounded-xl p-6 border" style={{ backgroundColor: '#166534', borderColor: '#16a34a' }}>
                  <pre className="whitespace-pre-wrap font-sans leading-relaxed" style={{ color: '#ffffff' }}>
                    {generatedEmail}
                  </pre>
                </div>
              ) : (
                <div className="flex flex-col items-center justify-center h-64" style={{ color: '#bbf7d0' }}>
                  <Mail className="w-16 h-16 mb-4 opacity-50" />
                  <p className="text-lg">{t('emailWillAppearHere')}</p>
                  <p className="text-sm mt-2">{t('getStartedPrompt')}</p>
                </div>
              )}
            </div>

            {/* Tips */}
            <div className="rounded-2xl p-6 border" style={{ backgroundColor: '#15803d', borderColor: '#f97316' }}>
              <h3 className="font-semibold mb-3" style={{ color: '#fdba74' }}>{t('proTips')}</h3>
              <ul className="text-sm space-y-2" style={{ color: '#dcfce7' }}>
                <li>{t('tipBeSpecific')}</li>
                <li>{t('tipIncludeDetails')}</li>
                <li>{t('tipTryTones')}</li>
                <li>{t('tipAddContext')}</li>
                <li>{t('tipLanguage')}</li>
              </ul>
            </div>
          </div>
        </div>
      </div>
      
      <style jsx>{`
        .slider::-webkit-slider-thumb {
          appearance: none;
          height: 20px;
          width: 20px;
          border-radius: 50%;
          background: #f97316;
          cursor: pointer;
          border: 2px solid #ffffff;
          box-shadow: 0 2px 4px rgba(0,0,0,0.2);
        }
        
        .slider::-moz-range-thumb {
          height: 20px;
          width: 20px;
          border-radius: 50%;
          background: #f97316;
          cursor: pointer;
          border: 2px solid #ffffff;
          box-shadow: 0 2px 4px rgba(0,0,0,0.2);
        }

        textarea::placeholder {
          color: #bbf7d0 !important;
        }

        input::placeholder {
          color: #bbf7d0 !important;
        }
      `}</style>
    </div>
  );
}
