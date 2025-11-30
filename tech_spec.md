# Auror Academy: Technical Requirements & Implementation Plan
## Version 1.0 — "Ship It" Edition

---

# Executive Summary

This document provides a complete technical roadmap for building and launching Auror Academy v1. The guiding philosophy is **radical simplicity**: we build the minimum viable product that delivers a complete, playable, educational experience for Mission 1. Everything else waits for v2.

**What v1 includes:**
- Single playable case ("The Vanishing Violinist")
- All five investigation phases fully functional
- Confirmation bias tracking and visualization
- Calibration scoring (for this single case)
- Complete case review with learning content
- Polished "magical document" visual theme

**What v1 explicitly excludes:**
- User accounts or authentication
- Progress persistence (refresh = restart)
- Multiple cases
- LLM integration
- Backend server
- Mobile-first design (desktop-first, responsive as bonus)

**Estimated development time:** 3-5 days for experienced React developer, 7-10 days for intermediate level.

---

# Part I: Technical Stack

## Chosen Technologies

| Layer | Technology | Rationale |
|-------|------------|-----------|
| Framework | React 18 + TypeScript | Industry standard, strong typing catches bugs early |
| Build Tool | Vite | Fast dev server, zero-config, modern defaults |
| Styling | Tailwind CSS | Rapid UI development, easy theming |
| State | React Context + useReducer | Simple, no external dependencies, sufficient for this scope |
| Routing | None (single-page with phases) | Over-engineering to add router for 6 "pages" |
| Testing | None for v1 | Ship fast, add tests when we have users |
| Deployment | Vercel or Netlify | Free tier, zero-config deployment from GitHub |

## Why NOT These Alternatives

**Redux/Zustand/Jotai:** Overkill. Our state is simple: current phase, selected hypotheses, collected evidence, probability assignments. React Context handles this fine.

**Next.js/Remix:** We have no backend, no SSR needs, no SEO requirements. Pure client-side React is simpler.

**CSS Modules/Styled Components:** Tailwind is faster for prototyping and the utility classes map well to our component-based UI.

**React Router:** We have 6 phases in a linear flow. A simple `currentPhase` state variable and conditional rendering is cleaner than URL-based routing for this use case.

---

# Part II: Project Structure

```
auror-academy/
├── index.html
├── package.json
├── tsconfig.json
├── tailwind.config.js
├── vite.config.ts
├── public/
│   └── fonts/                    # Custom fonts if needed
└── src/
    ├── main.tsx                  # Entry point
    ├── App.tsx                   # Main app shell, phase routing
    ├── index.css                 # Tailwind imports + custom styles
    │
    ├── types/
    │   └── game.ts               # All TypeScript interfaces
    │
    ├── data/
    │   └── mission1.ts           # Complete case data as typed object
    │
    ├── context/
    │   └── GameContext.tsx       # Game state provider + reducer
    │
    ├── hooks/
    │   └── useGame.ts            # Hook to access game context
    │
    ├── utils/
    │   ├── scoring.ts            # Confirmation bias + calibration calculations
    │   └── helpers.ts            # Misc utility functions
    │
    └── components/
        ├── layout/
        │   ├── GameShell.tsx     # Overall layout wrapper
        │   └── Header.tsx        # Case title, phase indicator
        │
        ├── phases/
        │   ├── Briefing.tsx      # Phase 1: Case file display
        │   ├── HypothesisFormation.tsx   # Phase 2
        │   ├── Investigation.tsx # Phase 3: Core gameplay
        │   ├── Prediction.tsx    # Phase 4: Lock-in predictions
        │   ├── Resolution.tsx    # Phase 5: Truth revealed
        │   └── CaseReview.tsx    # Phase 6: Learning consolidation
        │
        └── ui/
            ├── Button.tsx        # Styled button component
            ├── Card.tsx          # Parchment-style card container
            ├── ProbabilitySlider.tsx     # Slider that shows percentage
            ├── EvidenceCard.tsx  # Display collected evidence
            ├── ProgressIndicator.tsx     # Shows current phase
            └── Modal.tsx         # For evidence popups
```

**Total: ~20 files.** This is manageable. Resist the urge to add more abstraction layers.

---

# Part III: Data Structures

All TypeScript interfaces live in `src/types/game.ts`. Define these first—they're the contract for everything else.

```typescript
// src/types/game.ts

// ============================================
// CASE DATA TYPES (static, from mission1.ts)
// ============================================

export interface CaseData {
  id: string;
  title: string;
  subtitle: string;
  briefing: BriefingData;
  hypotheses: HypothesisData[];
  investigationActions: InvestigationAction[];
  resolution: ResolutionData;
  biasLessons: BiasLesson[];
}

export interface BriefingData {
  date: string;
  location: string;
  victim: string;
  status: string;
  summary: string;           // Main incident description
  healerReport: string;      // Technical details
  initialWitness: {
    name: string;
    statement: string;
  };
  personsOfInterest: PersonOfInterest[];
  mentorIntro: string;       // Senior Auror's opening words
}

export interface PersonOfInterest {
  id: string;
  name: string;
  description: string;
}

export interface HypothesisData {
  id: string;
  label: string;              // Short name: "Victor Ashworth"
  description: string;        // Full theory: "The jealous ex-partner cast the curse"
  isCorrect: boolean;         // Is this the true answer?
  isAlwaysAvailable?: boolean; // For "Something else" option
}

export interface InvestigationAction {
  id: string;
  title: string;
  description: string;        // What you'll do
  cost: number;               // Investigation Points
  category: 'location' | 'witness' | 'records';
  evidence: EvidenceData;
  // Which hypotheses does this evidence relate to?
  hypothesisImpact: {
    hypothesisId: string;
    impact: 'supports' | 'weakens' | 'neutral';
    weight: number;           // 1-3, how strongly
  }[];
}

export interface EvidenceData {
  title: string;
  content: string;            // The actual evidence text (can include line breaks)
  interpretation: string;     // What it means
  isCritical?: boolean;       // Highlight important evidence
}

export interface ResolutionData {
  truthSummary: string;       // What actually happened
  culprit: string;            // Who did it
  correctHypothesisId: string;
  explanationOfDifficulty: string;  // Why this case was tricky
}

export interface BiasLesson {
  biasName: string;
  explanation: string;
  howItApplied: string;       // How it showed up in this case
  counterTechnique: string;   // How to fight it
  realWorldExample: string;   // Application outside the game
}

// ============================================
// PLAYER STATE TYPES (dynamic, in context)
// ============================================

export type GamePhase = 
  | 'briefing' 
  | 'hypothesis' 
  | 'investigation' 
  | 'prediction' 
  | 'resolution' 
  | 'review';

export interface PlayerState {
  currentPhase: GamePhase;
  
  // Phase 2: Hypothesis Formation
  selectedHypotheses: string[];           // IDs of hypotheses player selected
  initialProbabilities: Record<string, number>;  // hypothesisId -> probability (0-100)
  
  // Phase 3: Investigation
  investigationPointsRemaining: number;
  collectedEvidenceIds: string[];         // IDs of evidence collected
  
  // Phase 4: Prediction
  finalProbabilities: Record<string, number>;
  confidenceLevel: number;                // 1-5 scale
  
  // Computed after resolution (for display in review)
  scores: PlayerScores | null;
}

export interface PlayerScores {
  // Did they include the correct answer in their hypotheses?
  correctHypothesisSelected: boolean;
  
  // Initial probability assigned to correct answer
  initialProbabilityOnCorrect: number;
  
  // Final probability assigned to correct answer
  finalProbabilityOnCorrect: number;
  
  // Confirmation bias: % of investigation focused on top hypothesis
  confirmationBiasScore: number;
  
  // Which hypothesis they investigated most
  mostInvestigatedHypothesis: string;
  
  // Did they find the critical evidence?
  foundCriticalEvidence: boolean;
  
  // List of evidence they missed
  missedCriticalEvidence: string[];
}

// ============================================
// ACTION TYPES (for reducer)
// ============================================

export type GameAction =
  | { type: 'START_GAME' }
  | { type: 'ADVANCE_PHASE' }
  | { type: 'GO_TO_PHASE'; phase: GamePhase }
  | { type: 'SELECT_HYPOTHESIS'; hypothesisId: string }
  | { type: 'DESELECT_HYPOTHESIS'; hypothesisId: string }
  | { type: 'SET_INITIAL_PROBABILITY'; hypothesisId: string; value: number }
  | { type: 'COLLECT_EVIDENCE'; actionId: string; cost: number }
  | { type: 'SET_FINAL_PROBABILITY'; hypothesisId: string; value: number }
  | { type: 'SET_CONFIDENCE'; level: number }
  | { type: 'CALCULATE_SCORES'; caseData: CaseData }
  | { type: 'RESET_GAME' };
```

---

# Part IV: State Management

We use React Context with useReducer. This gives us predictable state updates without external dependencies.

```typescript
// src/context/GameContext.tsx

import React, { createContext, useReducer, ReactNode } from 'react';
import { PlayerState, GameAction, GamePhase, CaseData } from '../types/game';
import { calculateScores } from '../utils/scoring';

// Initial state when game starts
const initialState: PlayerState = {
  currentPhase: 'briefing',
  selectedHypotheses: [],
  initialProbabilities: {},
  investigationPointsRemaining: 7,  // Mission 1 budget
  collectedEvidenceIds: [],
  finalProbabilities: {},
  confidenceLevel: 3,
  scores: null,
};

// The reducer handles all state transitions
function gameReducer(state: PlayerState, action: GameAction): PlayerState {
  switch (action.type) {
    case 'START_GAME':
      return { ...initialState };

    case 'ADVANCE_PHASE': {
      const phaseOrder: GamePhase[] = [
        'briefing', 'hypothesis', 'investigation', 
        'prediction', 'resolution', 'review'
      ];
      const currentIndex = phaseOrder.indexOf(state.currentPhase);
      const nextPhase = phaseOrder[currentIndex + 1] || state.currentPhase;
      return { ...state, currentPhase: nextPhase };
    }

    case 'GO_TO_PHASE':
      return { ...state, currentPhase: action.phase };

    case 'SELECT_HYPOTHESIS':
      return {
        ...state,
        selectedHypotheses: [...state.selectedHypotheses, action.hypothesisId],
        // Initialize probability to 0 when selected
        initialProbabilities: {
          ...state.initialProbabilities,
          [action.hypothesisId]: 0,
        },
      };

    case 'DESELECT_HYPOTHESIS': {
      const newSelected = state.selectedHypotheses.filter(
        id => id !== action.hypothesisId
      );
      const newProbs = { ...state.initialProbabilities };
      delete newProbs[action.hypothesisId];
      return {
        ...state,
        selectedHypotheses: newSelected,
        initialProbabilities: newProbs,
      };
    }

    case 'SET_INITIAL_PROBABILITY':
      return {
        ...state,
        initialProbabilities: {
          ...state.initialProbabilities,
          [action.hypothesisId]: action.value,
        },
      };

    case 'COLLECT_EVIDENCE':
      return {
        ...state,
        collectedEvidenceIds: [...state.collectedEvidenceIds, action.actionId],
        investigationPointsRemaining: state.investigationPointsRemaining - action.cost,
      };

    case 'SET_FINAL_PROBABILITY':
      return {
        ...state,
        finalProbabilities: {
          ...state.finalProbabilities,
          [action.hypothesisId]: action.value,
        },
      };

    case 'SET_CONFIDENCE':
      return { ...state, confidenceLevel: action.level };

    case 'CALCULATE_SCORES':
      return {
        ...state,
        scores: calculateScores(state, action.caseData),
      };

    case 'RESET_GAME':
      return { ...initialState };

    default:
      return state;
  }
}

// Create context
interface GameContextType {
  state: PlayerState;
  dispatch: React.Dispatch<GameAction>;
}

export const GameContext = createContext<GameContextType | null>(null);

// Provider component
export function GameProvider({ children }: { children: ReactNode }) {
  const [state, dispatch] = useReducer(gameReducer, initialState);
  
  return (
    <GameContext.Provider value={{ state, dispatch }}>
      {children}
    </GameContext.Provider>
  );
}
```

```typescript
// src/hooks/useGame.ts

import { useContext } from 'react';
import { GameContext } from '../context/GameContext';

export function useGame() {
  const context = useContext(GameContext);
  if (!context) {
    throw new Error('useGame must be used within GameProvider');
  }
  return context;
}
```

---

# Part V: Scoring Logic

The scoring utilities calculate confirmation bias and other metrics after the player completes the case.

```typescript
// src/utils/scoring.ts

import { PlayerState, CaseData, PlayerScores } from '../types/game';

export function calculateScores(
  playerState: PlayerState,
  caseData: CaseData
): PlayerScores {
  const correctHypothesis = caseData.hypotheses.find(h => h.isCorrect);
  const correctId = correctHypothesis?.id || '';

  // 1. Did they select the correct hypothesis?
  const correctHypothesisSelected = playerState.selectedHypotheses.includes(correctId);

  // 2. Initial probability on correct answer
  const initialProbabilityOnCorrect = playerState.initialProbabilities[correctId] || 0;

  // 3. Final probability on correct answer
  const finalProbabilityOnCorrect = playerState.finalProbabilities[correctId] || 0;

  // 4. Confirmation bias calculation
  const { confirmationBiasScore, mostInvestigatedHypothesis } = 
    calculateConfirmationBias(playerState, caseData);

  // 5. Critical evidence check
  const criticalActions = caseData.investigationActions.filter(
    a => a.evidence.isCritical
  );
  const criticalIds = criticalActions.map(a => a.id);
  const foundCriticalIds = playerState.collectedEvidenceIds.filter(
    id => criticalIds.includes(id)
  );
  const missedCriticalEvidence = criticalActions
    .filter(a => !playerState.collectedEvidenceIds.includes(a.id))
    .map(a => a.title);

  return {
    correctHypothesisSelected,
    initialProbabilityOnCorrect,
    finalProbabilityOnCorrect,
    confirmationBiasScore,
    mostInvestigatedHypothesis,
    foundCriticalEvidence: foundCriticalIds.length === criticalIds.length,
    missedCriticalEvidence,
  };
}

function calculateConfirmationBias(
  playerState: PlayerState,
  caseData: CaseData
): { confirmationBiasScore: number; mostInvestigatedHypothesis: string } {
  
  // Find which hypothesis the player seemed to favor initially
  // (highest initial probability)
  let topHypothesisId = '';
  let topProbability = 0;
  
  for (const [hypId, prob] of Object.entries(playerState.initialProbabilities)) {
    if (prob > topProbability) {
      topProbability = prob;
      topHypothesisId = hypId;
    }
  }

  if (!topHypothesisId || playerState.collectedEvidenceIds.length === 0) {
    return { confirmationBiasScore: 0, mostInvestigatedHypothesis: 'N/A' };
  }

  // Count how many investigation actions related to the top hypothesis
  let actionsForTop = 0;
  let totalActions = playerState.collectedEvidenceIds.length;

  for (const actionId of playerState.collectedEvidenceIds) {
    const action = caseData.investigationActions.find(a => a.id === actionId);
    if (!action) continue;

    // Check if this action relates to the top hypothesis
    const relatedToTop = action.hypothesisImpact.some(
      impact => impact.hypothesisId === topHypothesisId
    );
    
    if (relatedToTop) {
      actionsForTop++;
    }
  }

  // Confirmation bias score: what % of investigation went to top hypothesis
  // Score of 100 = all investigation on one hypothesis
  // Score of 25 = evenly distributed across 4 hypotheses
  const confirmationBiasScore = Math.round((actionsForTop / totalActions) * 100);

  // Get the name of the most investigated hypothesis
  const topHypothesis = caseData.hypotheses.find(h => h.id === topHypothesisId);
  const mostInvestigatedHypothesis = topHypothesis?.label || 'Unknown';

  return { confirmationBiasScore, mostInvestigatedHypothesis };
}

// Helper: Check if probabilities sum to approximately 100
export function probabilitiesAreValid(probabilities: Record<string, number>): boolean {
  const sum = Object.values(probabilities).reduce((a, b) => a + b, 0);
  return sum >= 99 && sum <= 101; // Allow tiny rounding errors
}

// Helper: Get confirmation bias interpretation
export function getConfirmationBiasInterpretation(score: number): {
  level: 'low' | 'medium' | 'high';
  message: string;
} {
  if (score <= 40) {
    return {
      level: 'low',
      message: 'Well-diversified investigation! You spread your effort across multiple theories.',
    };
  } else if (score <= 65) {
    return {
      level: 'medium',
      message: 'Some focus on your leading theory, but you explored alternatives too.',
    };
  } else {
    return {
      level: 'high',
      message: 'High confirmation bias detected. You focused heavily on your favorite theory.',
    };
  }
}
```

---

# Part VI: Component Implementation Guide

This section provides implementation guidance for each major component. Code is illustrative, not exhaustive—focus on the patterns.

## App Shell

```typescript
// src/App.tsx

import { GameProvider } from './context/GameContext';
import { GameShell } from './components/layout/GameShell';
import { useGame } from './hooks/useGame';
import { mission1 } from './data/mission1';

// Phase components
import { Briefing } from './components/phases/Briefing';
import { HypothesisFormation } from './components/phases/HypothesisFormation';
import { Investigation } from './components/phases/Investigation';
import { Prediction } from './components/phases/Prediction';
import { Resolution } from './components/phases/Resolution';
import { CaseReview } from './components/phases/CaseReview';

function GameContent() {
  const { state } = useGame();
  
  // Simple phase-based rendering (no router needed)
  const renderPhase = () => {
    switch (state.currentPhase) {
      case 'briefing':
        return <Briefing caseData={mission1} />;
      case 'hypothesis':
        return <HypothesisFormation caseData={mission1} />;
      case 'investigation':
        return <Investigation caseData={mission1} />;
      case 'prediction':
        return <Prediction caseData={mission1} />;
      case 'resolution':
        return <Resolution caseData={mission1} />;
      case 'review':
        return <CaseReview caseData={mission1} />;
      default:
        return <Briefing caseData={mission1} />;
    }
  };

  return (
    <GameShell caseTitle={mission1.title} currentPhase={state.currentPhase}>
      {renderPhase()}
    </GameShell>
  );
}

export default function App() {
  return (
    <GameProvider>
      <GameContent />
    </GameProvider>
  );
}
```

## Briefing Phase

The simplest phase—just display case information.

```typescript
// src/components/phases/Briefing.tsx

import { CaseData } from '../../types/game';
import { useGame } from '../../hooks/useGame';
import { Card } from '../ui/Card';
import { Button } from '../ui/Button';

interface Props {
  caseData: CaseData;
}

export function Briefing({ caseData }: Props) {
  const { dispatch } = useGame();
  const { briefing } = caseData;

  return (
    <div className="space-y-6">
      {/* Case File Header */}
      <Card variant="official">
        <div className="text-center border-b border-amber-700/30 pb-4 mb-4">
          <h1 className="text-2xl font-serif text-amber-900">
            MINISTRY OF MAGIC — AUROR DEPARTMENT
          </h1>
          <p className="text-amber-700">INCIDENT REPORT #{caseData.id}</p>
        </div>

        <div className="grid grid-cols-2 gap-4 text-sm mb-6">
          <div><strong>Date:</strong> {briefing.date}</div>
          <div><strong>Location:</strong> {briefing.location}</div>
          <div><strong>Victim:</strong> {briefing.victim}</div>
          <div><strong>Status:</strong> {briefing.status}</div>
        </div>

        <div className="space-y-4">
          <section>
            <h2 className="font-bold text-amber-900 mb-2">SUMMARY OF INCIDENT</h2>
            <p className="text-amber-800 leading-relaxed">{briefing.summary}</p>
          </section>

          <section>
            <h2 className="font-bold text-amber-900 mb-2">HEALER'S REPORT</h2>
            <p className="text-amber-800 italic">"{briefing.healerReport}"</p>
          </section>

          <section>
            <h2 className="font-bold text-amber-900 mb-2">INITIAL WITNESS STATEMENT</h2>
            <p className="text-amber-700 font-medium">{briefing.initialWitness.name}:</p>
            <p className="text-amber-800 italic">"{briefing.initialWitness.statement}"</p>
          </section>

          <section>
            <h2 className="font-bold text-amber-900 mb-2">PERSONS OF INTEREST</h2>
            <ul className="space-y-2">
              {briefing.personsOfInterest.map((person, index) => (
                <li key={person.id} className="text-amber-800">
                  <strong>{index + 1}. {person.name}</strong> — {person.description}
                </li>
              ))}
            </ul>
          </section>
        </div>
      </Card>

      {/* Mentor Introduction */}
      <Card variant="speech">
        <p className="text-amber-800 italic leading-relaxed">
          {briefing.mentorIntro}
        </p>
        <p className="text-right text-amber-700 mt-2">— Senior Auror Moody</p>
      </Card>

      {/* Proceed Button */}
      <div className="flex justify-end">
        <Button onClick={() => dispatch({ type: 'ADVANCE_PHASE' })}>
          Begin Investigation →
        </Button>
      </div>
    </div>
  );
}
```

## Hypothesis Formation Phase

Key challenge: probability sliders that sum to 100%.

```typescript
// src/components/phases/HypothesisFormation.tsx

import { useState } from 'react';
import { CaseData } from '../../types/game';
import { useGame } from '../../hooks/useGame';
import { probabilitiesAreValid } from '../../utils/scoring';
import { Card } from '../ui/Card';
import { Button } from '../ui/Button';
import { ProbabilitySlider } from '../ui/ProbabilitySlider';

interface Props {
  caseData: CaseData;
}

export function HypothesisFormation({ caseData }: Props) {
  const { state, dispatch } = useGame();
  const { hypotheses } = caseData;

  const selectedCount = state.selectedHypotheses.length;
  const canProceed = selectedCount >= 3 && 
    probabilitiesAreValid(state.initialProbabilities);

  // Toggle hypothesis selection
  const toggleHypothesis = (id: string) => {
    if (state.selectedHypotheses.includes(id)) {
      dispatch({ type: 'DESELECT_HYPOTHESIS', hypothesisId: id });
    } else {
      dispatch({ type: 'SELECT_HYPOTHESIS', hypothesisId: id });
    }
  };

  // Update probability
  const setProbability = (id: string, value: number) => {
    dispatch({ type: 'SET_INITIAL_PROBABILITY', hypothesisId: id, value });
  };

  // Calculate sum for display
  const probabilitySum = Object.values(state.initialProbabilities)
    .reduce((a, b) => a + b, 0);

  return (
    <div className="space-y-6">
      <Card>
        <h2 className="text-xl font-serif text-amber-900 mb-4">
          Hypothesis Formation
        </h2>
        <p className="text-amber-800 mb-4">
          Before investigating, consider all the possibilities. Select at least 
          3 hypotheses you want to track, then assign probability estimates.
        </p>
        <p className="text-amber-700 text-sm">
          Tip: Always leave some probability for "something else" — 
          the true answer might not be on your list.
        </p>
      </Card>

      {/* Hypothesis Selection */}
      <div className="space-y-3">
        {hypotheses.map(hypothesis => {
          const isSelected = state.selectedHypotheses.includes(hypothesis.id);
          const probability = state.initialProbabilities[hypothesis.id] || 0;

          return (
            <Card 
              key={hypothesis.id}
              variant={isSelected ? 'selected' : 'default'}
              onClick={() => toggleHypothesis(hypothesis.id)}
              className="cursor-pointer"
            >
              <div className="flex items-start gap-4">
                {/* Checkbox indicator */}
                <div className={`
                  w-6 h-6 rounded border-2 flex items-center justify-center mt-1
                  ${isSelected 
                    ? 'bg-amber-600 border-amber-600 text-white' 
                    : 'border-amber-400'
                  }
                `}>
                  {isSelected && '✓'}
                </div>

                <div className="flex-1">
                  <h3 className="font-bold text-amber-900">{hypothesis.label}</h3>
                  <p className="text-amber-700 text-sm">{hypothesis.description}</p>

                  {/* Probability slider (only if selected) */}
                  {isSelected && (
                    <div className="mt-3" onClick={e => e.stopPropagation()}>
                      <ProbabilitySlider
                        value={probability}
                        onChange={(v) => setProbability(hypothesis.id, v)}
                      />
                    </div>
                  )}
                </div>
              </div>
            </Card>
          );
        })}
      </div>

      {/* Probability Sum Indicator */}
      {selectedCount > 0 && (
        <Card variant={probabilitySum === 100 ? 'success' : 'warning'}>
          <div className="flex justify-between items-center">
            <span>Total Probability:</span>
            <span className="text-2xl font-bold">
              {probabilitySum}%
              {probabilitySum !== 100 && (
                <span className="text-sm font-normal ml-2">
                  (must equal 100%)
                </span>
              )}
            </span>
          </div>
        </Card>
      )}

      {/* Proceed Button */}
      <div className="flex justify-between items-center">
        <span className="text-amber-700">
          {selectedCount < 3 
            ? `Select at least ${3 - selectedCount} more hypotheses`
            : probabilitySum !== 100
            ? 'Adjust probabilities to sum to 100%'
            : 'Ready to investigate'
          }
        </span>
        <Button 
          onClick={() => dispatch({ type: 'ADVANCE_PHASE' })}
          disabled={!canProceed}
        >
          Begin Investigation →
        </Button>
      </div>
    </div>
  );
}
```

## Investigation Phase

The core gameplay loop. Most complex component.

```typescript
// src/components/phases/Investigation.tsx

import { useState } from 'react';
import { CaseData, InvestigationAction } from '../../types/game';
import { useGame } from '../../hooks/useGame';
import { Card } from '../ui/Card';
import { Button } from '../ui/Button';
import { EvidenceCard } from '../ui/EvidenceCard';
import { Modal } from '../ui/Modal';

interface Props {
  caseData: CaseData;
}

export function Investigation({ caseData }: Props) {
  const { state, dispatch } = useGame();
  const [activeEvidence, setActiveEvidence] = useState<InvestigationAction | null>(null);
  const [showEvidenceModal, setShowEvidenceModal] = useState(false);

  const { investigationActions } = caseData;
  const ip = state.investigationPointsRemaining;

  // Group actions by category
  const locations = investigationActions.filter(a => a.category === 'location');
  const witnesses = investigationActions.filter(a => a.category === 'witness');
  const records = investigationActions.filter(a => a.category === 'records');

  // Check if action is already collected
  const isCollected = (id: string) => state.collectedEvidenceIds.includes(id);

  // Handle collecting evidence
  const collectEvidence = (action: InvestigationAction) => {
    if (ip < action.cost || isCollected(action.id)) return;
    
    dispatch({ 
      type: 'COLLECT_EVIDENCE', 
      actionId: action.id, 
      cost: action.cost 
    });
    setActiveEvidence(action);
    setShowEvidenceModal(true);
  };

  // Render action button
  const renderAction = (action: InvestigationAction) => {
    const collected = isCollected(action.id);
    const canAfford = ip >= action.cost;

    return (
      <Card
        key={action.id}
        variant={collected ? 'collected' : canAfford ? 'default' : 'disabled'}
        className={`cursor-pointer ${!canAfford && !collected ? 'opacity-50' : ''}`}
        onClick={() => !collected && canAfford && collectEvidence(action)}
      >
        <div className="flex justify-between items-start">
          <div>
            <h4 className="font-bold text-amber-900">
              {collected && '✓ '}{action.title}
            </h4>
            <p className="text-amber-700 text-sm">{action.description}</p>
          </div>
          <span className={`
            px-2 py-1 rounded text-sm font-bold
            ${collected ? 'bg-green-100 text-green-700' : 'bg-amber-100 text-amber-700'}
          `}>
            {collected ? 'Done' : `${action.cost} IP`}
          </span>
        </div>
      </Card>
    );
  };

  return (
    <div className="space-y-6">
      {/* IP Counter */}
      <Card variant="official">
        <div className="flex justify-between items-center">
          <h2 className="text-xl font-serif text-amber-900">Investigation</h2>
          <div className="text-right">
            <div className="text-2xl font-bold text-amber-900">
              {ip} <span className="text-base font-normal">IP remaining</span>
            </div>
            <div className="flex gap-1 mt-1">
              {Array.from({ length: 7 }).map((_, i) => (
                <div
                  key={i}
                  className={`w-4 h-4 rounded-full ${
                    i < ip ? 'bg-amber-500' : 'bg-amber-200'
                  }`}
                />
              ))}
            </div>
          </div>
        </div>
      </Card>

      {/* Actions Grid */}
      <div className="grid md:grid-cols-3 gap-6">
        {/* Locations */}
        <div>
          <h3 className="font-bold text-amber-900 mb-3 text-lg">📍 Locations</h3>
          <div className="space-y-2">
            {locations.map(renderAction)}
          </div>
        </div>

        {/* Witnesses */}
        <div>
          <h3 className="font-bold text-amber-900 mb-3 text-lg">👤 Witnesses</h3>
          <div className="space-y-2">
            {witnesses.map(renderAction)}
          </div>
        </div>

        {/* Records */}
        <div>
          <h3 className="font-bold text-amber-900 mb-3 text-lg">📜 Records</h3>
          <div className="space-y-2">
            {records.map(renderAction)}
          </div>
        </div>
      </div>

      {/* Collected Evidence Summary */}
      {state.collectedEvidenceIds.length > 0 && (
        <Card>
          <h3 className="font-bold text-amber-900 mb-3">Evidence Collected</h3>
          <div className="grid md:grid-cols-2 gap-2">
            {state.collectedEvidenceIds.map(id => {
              const action = investigationActions.find(a => a.id === id);
              if (!action) return null;
              return (
                <div 
                  key={id}
                  className="p-2 bg-amber-50 rounded text-sm cursor-pointer hover:bg-amber-100"
                  onClick={() => {
                    setActiveEvidence(action);
                    setShowEvidenceModal(true);
                  }}
                >
                  📋 {action.evidence.title}
                </div>
              );
            })}
          </div>
        </Card>
      )}

      {/* Proceed Button */}
      <div className="flex justify-between items-center">
        <span className="text-amber-700">
          {ip > 0 
            ? `You can still investigate (${ip} IP remaining)` 
            : 'No investigation points remaining'
          }
        </span>
        <Button 
          onClick={() => dispatch({ type: 'ADVANCE_PHASE' })}
          disabled={state.collectedEvidenceIds.length === 0}
        >
          Lock In & Make Predictions →
        </Button>
      </div>

      {/* Evidence Modal */}
      <Modal 
        isOpen={showEvidenceModal} 
        onClose={() => setShowEvidenceModal(false)}
      >
        {activeEvidence && (
          <EvidenceCard evidence={activeEvidence.evidence} />
        )}
      </Modal>
    </div>
  );
}
```

## Remaining Phases (Abbreviated)

**Prediction Phase:** Similar structure to HypothesisFormation, but uses `finalProbabilities` and adds a confidence slider. Reuses the `ProbabilitySlider` component.

**Resolution Phase:** Displays the truth from `caseData.resolution`, compares to player's predictions, triggers score calculation with `dispatch({ type: 'CALCULATE_SCORES', caseData })`.

**CaseReview Phase:** Displays scores from `state.scores`, shows confirmation bias visualization, bias lessons from `caseData.biasLessons`, and real-world applications. Includes "Play Again" button that dispatches `RESET_GAME`.

---

# Part VII: Implementation Schedule

## Day 1: Foundation

**Morning (3-4 hours):**
- [ ] Initialize Vite + React + TypeScript project
- [ ] Install and configure Tailwind CSS
- [ ] Create folder structure
- [ ] Define all TypeScript interfaces in `types/game.ts`
- [ ] Implement GameContext and useGame hook

**Afternoon (3-4 hours):**
- [ ] Create basic UI components: Button, Card
- [ ] Create GameShell layout component
- [ ] Create App.tsx with phase switching logic
- [ ] Verify basic app runs and displays placeholder content

**Deliverable:** App shell working, can manually switch phases, styled containers visible.

---

## Day 2: Briefing + Hypothesis Phases

**Morning (3-4 hours):**
- [ ] Write complete mission1.ts data file (briefing section)
- [ ] Implement Briefing component with full styling
- [ ] Test briefing display, "Begin Investigation" navigation

**Afternoon (3-4 hours):**
- [ ] Write hypotheses section of mission1.ts
- [ ] Implement HypothesisFormation component
- [ ] Implement ProbabilitySlider component
- [ ] Add probability sum validation
- [ ] Test hypothesis selection and probability assignment

**Deliverable:** Can read briefing and complete hypothesis phase.

---

## Day 3: Investigation Phase

**Full Day (6-8 hours):**
- [ ] Write all investigation actions in mission1.ts (10 actions)
- [ ] Implement Investigation component (action grid layout)
- [ ] Implement EvidenceCard component
- [ ] Implement Modal component for evidence popups
- [ ] Add IP tracking and deduction
- [ ] Add evidence collection state management
- [ ] Style the evidence display to feel substantial
- [ ] Test full investigation flow

**Deliverable:** Can investigate, collect evidence, see evidence details.

---

## Day 4: Prediction + Resolution + Scoring

**Morning (3-4 hours):**
- [ ] Implement Prediction component (reuse slider logic)
- [ ] Add confidence level selector
- [ ] Write resolution content in mission1.ts
- [ ] Implement Resolution component

**Afternoon (3-4 hours):**
- [ ] Implement calculateScores function
- [ ] Implement calculateConfirmationBias function
- [ ] Connect scoring to resolution phase
- [ ] Display prediction results in resolution

**Deliverable:** Can make predictions, see truth revealed, see basic scores.

---

## Day 5: Case Review + Polish

**Morning (3-4 hours):**
- [ ] Write bias lessons content in mission1.ts
- [ ] Implement CaseReview component
- [ ] Display confirmation bias visualization
- [ ] Display bias lessons
- [ ] Add "Play Again" functionality

**Afternoon (3-4 hours):**
- [ ] Visual polish pass (fonts, colors, spacing)
- [ ] Add progress indicator showing current phase
- [ ] Test complete game flow multiple times
- [ ] Fix any bugs discovered
- [ ] Deploy to Vercel/Netlify

**Deliverable:** Complete, playable, polished v1 deployed.

---

## Day 6+ (Buffer / Nice-to-Have)

If ahead of schedule:
- [ ] Add subtle animations (fade transitions between phases)
- [ ] Add sound effects (optional)
- [ ] Mobile responsive adjustments
- [ ] Shareable results ("I scored X on Auror Academy!")
- [ ] Local storage save (persist game state across refresh)

---

# Part VIII: Visual Design Specifications

## Color Palette

```css
/* Primary - Parchment/Paper tones */
--parchment-50: #fefdfb;
--parchment-100: #fdf9f0;
--parchment-200: #f5eedd;

/* Accent - Magical gold/amber */
--amber-500: #d97706;
--amber-600: #b45309;
--amber-700: #92400e;
--amber-800: #78350f;
--amber-900: #451a03;

/* Semantic */
--success: #059669;   /* Green for correct */
--warning: #d97706;   /* Amber for attention */
--danger: #dc2626;    /* Red for errors */
--info: #2563eb;      /* Blue for neutral info */
```

## Typography

```css
/* Headers: Serif font for "magical document" feel */
font-family: 'Crimson Text', Georgia, serif;

/* Body: Readable sans-serif */
font-family: 'Inter', system-ui, sans-serif;

/* Monospace (for evidence details): */
font-family: 'Fira Code', monospace;
```

## Card Variants

The Card component should support these variants:

| Variant | Usage | Styling |
|---------|-------|---------|
| `default` | Normal content | Light parchment background, subtle border |
| `official` | Case files, formal documents | Darker border, "classified" styling |
| `selected` | Selected hypothesis, active choice | Amber highlight border |
| `collected` | Evidence already gathered | Green checkmark, muted styling |
| `disabled` | Can't afford, unavailable | Grayed out, reduced opacity |
| `success` | Correct predictions | Green accent |
| `warning` | Needs attention | Amber accent |
| `speech` | Mentor quotes | Italicized, different background |

## Component Styling (Tailwind)

```typescript
// src/components/ui/Card.tsx

interface CardProps {
  children: React.ReactNode;
  variant?: 'default' | 'official' | 'selected' | 'collected' | 'disabled' | 'success' | 'warning' | 'speech';
  className?: string;
  onClick?: () => void;
}

const variantStyles = {
  default: 'bg-amber-50 border border-amber-200',
  official: 'bg-amber-50 border-2 border-amber-700 shadow-md',
  selected: 'bg-amber-100 border-2 border-amber-500 shadow-md',
  collected: 'bg-green-50 border border-green-300',
  disabled: 'bg-gray-100 border border-gray-300 opacity-60',
  success: 'bg-green-50 border border-green-400',
  warning: 'bg-amber-100 border border-amber-400',
  speech: 'bg-amber-100/50 border-l-4 border-amber-600',
};

export function Card({ children, variant = 'default', className = '', onClick }: CardProps) {
  return (
    <div 
      className={`rounded-lg p-4 ${variantStyles[variant]} ${className}`}
      onClick={onClick}
    >
      {children}
    </div>
  );
}
```

---

# Part IX: Testing Checklist

Before deploying, verify:

## Functional Tests

- [ ] Can read briefing and proceed to hypotheses
- [ ] Can select/deselect hypotheses
- [ ] Probability sliders work and sum validation displays correctly
- [ ] Can only proceed with 3+ hypotheses and sum=100%
- [ ] Investigation points decrement correctly
- [ ] Cannot collect same evidence twice
- [ ] Cannot collect evidence if IP insufficient
- [ ] Evidence modal displays correct content
- [ ] Prediction sliders work correctly
- [ ] Resolution shows correct information
- [ ] Scores calculate correctly (test with known inputs)
- [ ] Case review displays all sections
- [ ] "Play Again" resets game completely

## Edge Cases

- [ ] What if player selects exactly 3 hypotheses?
- [ ] What if player selects all hypotheses?
- [ ] What if player uses 0 IP (skips investigation)?
- [ ] What if player uses exactly 7 IP?
- [ ] What if probabilities are all 0 except one at 100%?
- [ ] What if player refreshes mid-game? (Expected: restart)

## Visual Tests

- [ ] All text readable on parchment backgrounds
- [ ] Buttons clearly clickable
- [ ] Selected states visually distinct
- [ ] Disabled states visually clear
- [ ] Evidence modal readable
- [ ] Works on 1920x1080 screen
- [ ] Works on 1366x768 screen
- [ ] No horizontal scrolling on desktop

---

# Part X: Deployment

## Recommended: Vercel

1. Push code to GitHub repository
2. Go to vercel.com, sign in with GitHub
3. Click "Import Project" → select repository
4. Accept defaults (Vite is auto-detected)
5. Click "Deploy"
6. Done. You'll get a URL like `auror-academy.vercel.app`

## Alternative: Netlify

1. Push code to GitHub
2. Go to netlify.com, sign in with GitHub
3. Click "Add new site" → "Import an existing project"
4. Select repository
5. Build command: `npm run build`
6. Publish directory: `dist`
7. Click "Deploy site"

## Post-Deployment

- [ ] Verify all phases work on deployed version
- [ ] Test on a few different devices/browsers
- [ ] Share link for feedback!

---

# Part XI: Future Enhancements (v2 Planning)

Documented here so they don't creep into v1:

## LLM Integration

**Dynamic Witness Interviews:** Replace scripted interviews with Claude-powered conversations. Players ask free-form questions; witnesses respond in character with their knowledge and biases.

**Hypothesis Evaluation:** Let players type custom hypotheses. LLM evaluates if they're meaningfully distinct.

**Personalized Feedback:** Generate custom case review based on the specific pattern of player decisions.

## Technical Enhancements

**User Accounts:** Save progress, track calibration across cases, leaderboards.

**Multiple Cases:** Add Missions 2-5 with progressive difficulty.

**Mobile App:** React Native version for mobile-first experience.

**Multiplayer:** Collaborative investigation or competitive solving.

## Content Enhancements

**Branching Narratives:** Some evidence reveals new suspects or changes available actions.

**Witness Credibility System:** Some witnesses lie or are mistaken. Players must evaluate reliability.

**Time Pressure:** Optional timed mode for advanced players.

---

# Quick Reference: Commands

```bash
# Create project
npm create vite@latest auror-academy -- --template react-ts

# Install dependencies
cd auror-academy
npm install
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

# Development
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

---

# Conclusion

This document provides everything needed to build and ship Auror Academy v1. The key is resisting scope creep—every feature not on this list is a v2 feature. Ship Mission 1, get player feedback, then iterate.

The most important metrics for v1 success:
1. Players complete the full case (retention)
2. Players understand they were affected by confirmation bias (learning)
3. Players want to play again or share with friends (engagement)

Good luck, and may your investigation be free of cognitive biases. 🧙‍♂️