const AGE_GROUPS = {
  young: {
    label: '3 a 5 años',
    tasks: [
      {
        type: 'count',
        prompt: '¿Cuántos 🍎 hay?',
        options: [2, 3, 5, 7],
        answer: 3,
        visual: '🍎🍎🍎'
      },
      {
        type: 'color',
        prompt: '¿Qué color es el sol?',
        options: ['Rojo', 'Amarillo', 'Verde', 'Azul'],
        answer: 'Amarillo'
      },
      {
        type: 'shape',
        prompt: '¿Qué figura tiene 3 lados?',
        options: ['Triángulo', 'Cuadrado', 'Círculo', 'Rectángulo'],
        answer: 'Triángulo'
      },
      {
        type: 'animal',
        prompt: '¿Qué animal dice “miau”?',
        options: ['Perro', 'Gato', 'Pato', 'Vaca'],
        answer: 'Gato'
      }
    ]
  },
  middle: {
    label: '6 a 8 años',
    tasks: [
      {
        type: 'math',
        prompt: '2 + 3 = ?',
        options: [4, 5, 6, 7],
        answer: 5
      },
      {
        type: 'letter',
        prompt: '¿Qué letra viene después de B?',
        options: ['A', 'C', 'D', 'E'],
        answer: 'C'
      },
      {
        type: 'word',
        prompt: '¿Qué palabra tiene 5 letras?',
        options: ['Sol', 'Casa', 'Luna', 'Mar'],
        answer: 'Casa'
      },
      {
        type: 'compare',
        prompt: '¿Cuál número es mayor?',
        options: [12, 9, 15, 6],
        answer: 15
      }
    ]
  },
  older: {
    label: '9 a 10 años',
    tasks: [
      {
        type: 'math',
        prompt: '7 + 8 = ?',
        options: [13, 15, 14, 16],
        answer: 15
      },
      {
        type: 'subtract',
        prompt: '12 - 5 = ?',
        options: [7, 6, 5, 8],
        answer: 7
      },
      {
        type: 'logic',
        prompt: 'Si 3 manzanas cuestan 6, ¿cuánto cuesta 1?',
        options: [1, 2, 3, 4],
        answer: 2
      },
      {
        type: 'sequence',
        prompt: '¿Qué sigue? 2, 4, 6, ?',
        options: [7, 8, 9, 10],
        answer: 8
      }
    ]
  }
};

const state = {
  ageGroup: null,
  score: 0,
  stars: 0,
  currentTask: null,
  currentOptions: [],
  answered: false
};

const startScreen = document.getElementById('start-screen');
const gameScreen = document.getElementById('game-screen');
const levelLabel = document.getElementById('level-label');
const scoreLabel = document.getElementById('score');
const starsLabel = document.getElementById('stars');
const questionText = document.getElementById('question-text');
const optionsContainer = document.getElementById('options');
const feedback = document.getElementById('feedback');
const nextBtn = document.getElementById('next-btn');
const restartBtn = document.getElementById('restart-btn');

function selectAge(ageKey) {
  state.ageGroup = ageKey;
  state.score = 0;
  state.stars = 0;
  state.answered = false;

  levelLabel.textContent = AGE_GROUPS[ageKey].label;
  scoreLabel.textContent = '0';
  starsLabel.textContent = '⭐';

  startScreen.classList.remove('active');
  gameScreen.classList.add('active');
  restartBtn.classList.remove('hidden');

  showNextTask();
}

function pickRandomTask() {
  const tasks = AGE_GROUPS[state.ageGroup].tasks;
  const index = Math.floor(Math.random() * tasks.length);
  return tasks[index];
}

function showNextTask() {
  state.currentTask = pickRandomTask();
  state.answered = false;
  feedback.textContent = '';
  feedback.style.background = 'transparent';
  nextBtn.classList.add('hidden');
  optionsContainer.innerHTML = '';

  const task = state.currentTask;
  questionText.textContent = task.prompt;

  task.options.forEach((option) => {
    const button = document.createElement('button');
    button.className = 'option-btn';
    button.textContent = String(option);
    button.type = 'button';

    button.addEventListener('click', () => {
      if (state.answered) return;
      state.answered = true;
      const isCorrect = option === task.answer;

      [...optionsContainer.children].forEach((btn) => {
        btn.disabled = true;
        if (btn.textContent === String(task.answer)) {
          btn.classList.add('correct');
        }
        if (btn === button && !isCorrect) {
          btn.classList.add('wrong');
        }
      });

      if (isCorrect) {
        state.score += 10;
        state.stars += 1;
        scoreLabel.textContent = String(state.score);
        starsLabel.textContent = '⭐'.repeat(state.stars);
        feedback.textContent = '¡Muy bien! ¡Correcto!';
        feedback.style.background = '#dfffe7';
      } else {
        feedback.textContent = `¡Casi! La respuesta correcta es ${task.answer}.`;
        feedback.style.background = '#ffe1e1';
      }

      nextBtn.classList.remove('hidden');
    });

    optionsContainer.appendChild(button);
  });
}

function restartGame() {
  startScreen.classList.add('active');
  gameScreen.classList.remove('active');
  restartBtn.classList.add('hidden');
  feedback.textContent = '';
  optionsContainer.innerHTML = '';
  questionText.textContent = '¿Listo para empezar?';
}

document.querySelectorAll('.age-btn').forEach((button) => {
  button.addEventListener('click', () => selectAge(button.dataset.age));
});

nextBtn.addEventListener('click', showNextTask);
restartBtn.addEventListener('click', restartGame);
