# treino-em-casa
import { useState, useEffect, useRef } from 'react';
import { Dumbbell, Clock, User, Filter, Edit, Trash2, Plus, Save, X, ArrowLeft, ArrowRight, Play, Pause, SkipForward, Info } from 'lucide-react';

export default function CalestheniaTreino() {
  // Estados para gerenciar os treinos
  const [treinoAtual, setTreinoAtual] = useState(0);
  const [modo, setModo] = useState('visualizar'); // 'visualizar', 'editar', 'adicionar', 'executar'
  const [treinoEditando, setTreinoEditando] = useState(null);
  const [filtroNivel, setFiltroNivel] = useState('todos');
  const [filtroParte, setFiltroParte] = useState('todos');
  const [exercicioDetalhes, setExercicioDetalhes] = useState(null);
  
  // Estados para o modo de execução
  const [exercicioAtual, setExercicioAtual] = useState(0);
  const [serieAtual, setSerieAtual] = useState(1);
  const [cronometro, setCronometro] = useState(0);
  const [emExercicio, setEmExercicio] = useState(true);
  const [pausado, setPausado] = useState(false);
  const timerRef = useRef(null);
  
  // Biblioteca de ilustrações de exercícios
  const ilustracoes = {
    "Flexões": (
      <svg viewBox="0 0 200 150" className="w-full h-full">
        <rect x="40" y="110" width="120" height="10" fill="#ccc" rx="5" />
        <g>
          <rect x="50" y="80" width="100" height="20" fill="#3b82f6" rx="10" />
          <circle cx="60" cy="80" r="10" fill="#1d4ed8" />
          <circle cx="140" cy="80" r="10" fill="#1d4ed8" />
          <rect x="80" y="60" width="40" height="10" fill="#3b82f6" rx="5" />
          <circle cx="80" cy="65" r="5" fill="#3b82f6" />
          <circle cx="120" cy="65" r="5" fill="#3b82f6" />
        </g>
        <line x1="60" y1="110" x2="60" y2="80" stroke="#1d4ed8" strokeWidth="4" />
        <line x1="140" y1="110" x2="140" y2="80" stroke="#1d4ed8" strokeWidth="4" />
      </svg>
    ),
    "Agachamentos": (
      <svg viewBox="0 0 200 150" className="w-full h-full">
        <rect x="40" y="120" width="120" height="10" fill="#ccc" rx="5" />
        <g>
          <circle cx="100" cy="50" r="15" fill="#3b82f6" />
          <rect x="85" y="65" width="30" height="40" fill="#3b82f6" rx="5" />
          <line x1="85" y1="75" x2="70" y2="110" stroke="#1d4ed8" strokeWidth="8" strokeLinecap="round" />
          <line x1="115" y1="75" x2="130" y2="110" stroke="#1d4ed8" strokeWidth="8" strokeLinecap="round" />
        </g>
      </svg>
    ),
    "Barras": (
      <svg viewBox="0 0 200 150" className="w-full h-full">
        <rect x="40" y="20" width="120" height="10" fill="#ccc" rx="5" />
        <g>
          <circle cx="100" cy="90" r="15" fill="#3b82f6" />
          <rect x="85" y="65" width="30" height="40" fill="#3b82f6" rx="5" />
          <line x1="85" y1="65" x2="60" y2="30" stroke="#1d4ed8" strokeWidth="8" strokeLinecap="round" />
          <line x1="115" y1="65" x2="140" y2="30" stroke="#1d4ed8" strokeWidth="8" strokeLinecap="round" />
        </g>
      </svg>
    ),
    "Mountain Climbers": (
      <svg viewBox="0 0 200 150" className="w-full h-full">
        <rect x="40" y="110" width="120" height="10" fill="#ccc" rx="5" />
        <g>
          <rect x="60" y="70" width="80" height="20" fill="#3b82f6" rx="10" />
          <circle cx="70" cy="70" r="10" fill="#1d4ed8" />
          <circle cx="130" cy="70" r="10" fill="#1d4ed8" />
          <line x1="70" y1="110" x2="70" y2="70" stroke="#1d4ed8" strokeWidth="4" />
          <line x1="130" y1="110" x2="130" y2="70" stroke="#1d4ed8" strokeWidth="4" />
          <line x1="100" y1="90" x2="70" y2="130" stroke="#3b82f6" strokeWidth="8" strokeLinecap="round" />
          <line x1="100" y1="90" x2="140" y2="100" stroke="#3b82f6" strokeWidth="8" strokeLinecap="round" />
        </g>
      </svg>
    ),
    "Prancha": (
      <svg viewBox="0 0 200 150" className="w-full h-full">
        <rect x="40" y="110" width="120" height="10" fill="#ccc" rx="5" />
        <g>
          <rect x="50" y="85" width="100" height="15" fill="#3b82f6" rx="7" />
          <circle cx="60" cy="85" r="10" fill="#1d4ed8" />
          <circle cx="140" cy="85" r="10" fill="#1d4ed8" />
          <line x1="60" y1="110" x2="60" y2="85" stroke="#1d4ed8" strokeWidth="4" />
          <line x1="140" y1="110" x2="140" y2="85" stroke="#1d4ed8" strokeWidth="4" />
        </g>
      </svg>
    ),
    "Afundos": (
      <svg viewBox="0 0 200 150" className="w-full h-full">
        <rect x="40" y="120" width="120" height="10" fill="#ccc" rx="5" />
        <g>
          <circle cx="80" cy="60" r="15" fill="#3b82f6" />
          <rect x="65" y="75" width="30" height="25" fill="#3b82f6" rx="5" />
          <line x1="80" y1="100" x2="60" y2="120" stroke="#1d4ed8" strokeWidth="8" strokeLinecap="round" />
          <line x1="80" y1="100" x2="130" y2="90" stroke="#1d4ed8" strokeWidth="8" strokeLinecap="round" />
        </g>
      </svg>
    ),
    "Elevação de Panturrilha": (
      <svg viewBox="0 0 200 150" className="w-full h-full">
        <rect x="80" y="120" width="40" height="10" fill="#ccc" rx="5" />
        <g>
          <circle cx="100" cy="50" r="15" fill="#3b82f6" />
          <rect x="85" y="65" width="30" height="40" fill="#3b82f6" rx="5" />
          <line x1="85" y1="105" x2="85" y2="120" stroke="#1d4ed8" strokeWidth="8" strokeLinecap="round" />
          <line x1="115" y1="105" x2="115" y2="120" stroke="#1d4ed8" strokeWidth="8" strokeLinecap="round" />
          <line x1="85" y1="120" x2="77" y2="115" stroke="#a855f7" strokeWidth="6" strokeLinecap="round" />
          <line x1="115" y1="120" x2="123" y2="115" stroke="#a855f7" strokeWidth="6" strokeLinecap="round" />
        </g>
      </svg>
    ),
    "Agachamento Búlgaro": (
      <svg viewBox="0 0 200 150" className="w-full h-full">
        <rect x="40" y="120" width="70" height="10" fill="#ccc" rx="5" />
        <rect x="130" y="100" width="30" height="20" fill="#888" rx="5" />
        <g>
          <circle cx="90" cy="50" r="15" fill="#3b82f6" />
          <rect x="75" y="65" width="30" height="35" fill="#3b82f6" rx="5" />
          <line x1="75" y1="75" x2="60" y2="120" stroke="#1d4ed8" strokeWidth="8" strokeLinecap="round" />
          <line x1="105" y1="75" x2="140" y2="100" stroke="#1d4ed8" strokeWidth="8" strokeLinecap="round" />
        </g>
      </svg>
    ),
    "Flexões Diamante": (
      <svg viewBox="0 0 200 150" className="w-full h-full">
        <rect x="40" y="110" width="120" height="10" fill="#ccc" rx="5" />
        <g>
          <rect x="50" y="80" width="100" height="20" fill="#3b82f6" rx="10" />
          <circle cx="60" cy="80" r="10" fill="#1d4ed8" />
          <circle cx="140" cy="80" r="10" fill="#1d4ed8" />
          <rect x="85" y="65" width="30" height="10" fill="#3b82f6" rx="5" />
          <polygon points="100,70 90,80 110,80" fill="#a855f7" />
        </g>
        <line x1="60" y1="110" x2="60" y2="80" stroke="#1d4ed8" strokeWidth="4" />
        <line x1="140" y1="110" x2="140" y2="80" stroke="#1d4ed8" strokeWidth="4" />
      </svg>
    ),
    "Flexões Declinadas": (
      <svg viewBox="0 0 200 150" className="w-full h-full">
        <rect x="40" y="110" width="80" height="10" fill="#ccc" rx="5" />
        <rect x="120" y="90" width="40" height="20" fill="#888" rx="5" />
        <g>
          <rect x="50" y="80" width="100" height="20" fill="#3b82f6" rx="10" />
          <circle cx="60" cy="80" r="10" fill="#1d4ed8" />
          <circle cx="140" cy="80" r="10" fill="#1d4ed8" />
          <rect x="80" y="60" width="40" height="10" fill="#3b82f6" rx="5" />
        </g>
        <line x1="60" y1="110" x2="60" y2="80" stroke="#1d4ed8" strokeWidth="4" />
        <line x1="140" y1="90" x2="140" y2="80" stroke="#1d4ed8" strokeWidth="4" />
      </svg>
    ),
    "Barras Pronadas": (
      <svg viewBox="0 0 200 150" className="w-full h-full">
        <rect x="40" y="20" width="120" height="10" fill="#ccc" rx="5" />
        <g>
          <circle cx="100" cy="90" r="15" fill="#3b82f6" />
          <rect x="85" y="65" width="30" height="40" fill="#3b82f6" rx="5" />
          <line x1="85" y1="65" x2="60" y2="30" stroke="#1d4ed8" strokeWidth="8" strokeLinecap="round" />
          <line x1="115" y1="65" x2="140" y2="30" stroke="#1d4ed8" strokeWidth="8" strokeLinecap="round" />
          <line x1="60" y1="30" x2="50" y2="25" stroke="#a855f7" strokeWidth="4" />
          <line x1="140" y1="30" x2="150" y2="25" stroke="#a855f7" strokeWidth="4" />
        </g>
      </svg>
    ),
    "Dips": (
      <svg viewBox="0 0 200 150" className="w-full h-full">
        <rect x="40" y="60" width="40" height="10" fill="#ccc" rx="5" />
        <rect x="120" y="60" width="40" height="10" fill="#ccc" rx="5" />
        <g>
          <circle cx="100" cy="90" r="15" fill="#3b82f6" />
          <rect x="85" y="65" width="30" height="40" fill="#3b82f6" rx="5" />
          <line x1="85" y1="65" x2="60" y2="60" stroke="#1d4ed8" strokeWidth="8" strokeLinecap="round" />
          <line x1="115" y1="65" x2="140" y2="60" stroke="#1d4ed8" strokeWidth="8" strokeLinecap="round" />
        </g>
      </svg>
    ),
    "Flexões Archer": (
      <svg viewBox="0 0 200 150" className="w-full h-full">
        <rect x="40" y="110" width="120" height="10" fill="#ccc" rx="5" />
        <g>
          <rect x="50" y="80" width="100" height="20" fill="#3b82f6" rx="10" />
          <circle cx="60" cy="80" r="10" fill="#1d4ed8" />
          <circle cx="140" cy="80" r="10" fill="#1d4ed8" />
          <rect x="80" y="60" width="40" height="10" fill="#3b82f6" rx="5" />
        </g>
        <line x1="60" y1="110" x2="60" y2="80" stroke="#1d4ed8" strokeWidth="4" />
        <line x1="140" y1="110" x2="140" y2="80" stroke="#a855f7" strokeWidth="6" />
        <line x1="140" y1="80" x2="170" y2="80" stroke="#a855f7" strokeWidth="4" />
      </svg>
    ),
    "Novo Exercício": (
      <svg viewBox="0 0 200 150" className="w-full h-full">
        <circle cx="100" cy="75" r="50" fill="#e5e7eb" stroke="#9ca3af" strokeWidth="4" strokeDasharray="5,5" />
        <text x="100" y="80" textAnchor="middle" fontSize="16" fill="#6b7280">
          Ilustração
        </text>
      </svg>
    )
  };
  
  // Função para obter a ilustração de um exercício
  const getIlustracao = (nomeExercicio) => {
    return ilustracoes[nomeExercicio] || ilustracoes["Novo Exercício"];
  };
  
  // Estado para treinos
  const [treinos, setTreinos] = useState([
    {
      id: 1,
      nome: "Treino Completo",
      nivel: "intermediário",
      parte: "corpo completo",
      duracao: 45,
      exercicios: [
        { nome: "Flexões", series: 3, repeticoes: 10, descanso: 60 },
        { nome: "Agachamentos", series: 3, repeticoes: 15, descanso: 60 },
        { nome: "Barras", series: 3, repeticoes: 8, descanso: 90 },
        { nome: "Mountain Climbers", series: 3, repeticoes: 20, descanso: 45 },
        { nome: "Prancha", series: 3, repeticoes: "30s", descanso: 60 }
      ]
    },
    {
      id: 2,
      nome: "Treino de Pernas",
      nivel: "iniciante",
      parte: "pernas",
      duracao: 30,
      exercicios: [
        { nome: "Agachamentos", series: 4, repeticoes: 15, descanso: 60 },
        { nome: "Afundos", series: 3, repeticoes: 10, descanso: 60 },
        { nome: "Elevação de Panturrilha", series: 3, repeticoes: 20, descanso: 45 },
        { nome: "Agachamento Búlgaro", series: 3, repeticoes: 12, descanso: 60 }
      ]
    },
    {
      id: 3,
      nome: "Treino de Braços e Peito",
      nivel: "avançado",
      parte: "superior",
      duracao: 40,
      exercicios: [
        { nome: "Flexões Diamante", series: 4, repeticoes: 12, descanso: 60 },
        { nome: "Flexões Declinadas", series: 4, repeticoes: 10, descanso: 90 },
        { nome: "Barras Pronadas", series: 4, repeticoes: 8, descanso: 90 },
        { nome: "Dips", series: 4, repeticoes: 10, descanso: 90 },
        { nome: "Flexões Archer", series: 3, repeticoes: 8, descanso: 90 }
      ]
    }
  ]);

  // Função para filtrar treinos
  const treinosFiltrados = treinos.filter(treino => 
    (filtroNivel === 'todos' || treino.nivel === filtroNivel) && 
    (filtroParte === 'todos' || treino.parte === filtroParte)
  );

  // Funções para navegar pelos treinos
  const proximoTreino = () => {
    if (treinoAtual < treinosFiltrados.length - 1) {
      setTreinoAtual(treinoAtual + 1);
    } else {
      setTreinoAtual(0);
    }
  };

  const treinoAnterior = () => {
    if (treinoAtual > 0) {
      setTreinoAtual(treinoAtual - 1);
    } else {
      setTreinoAtual(treinosFiltrados.length - 1);
    }
  };

  // Funções para editar treinos
  const iniciarEdicao = (treino) => {
    setTreinoEditando({...treino});
    setModo('editar');
  };

  const cancelarEdicao = () => {
    setTreinoEditando(null);
    setModo('visualizar');
  };

  const salvarTreino = () => {
    if (modo === 'editar') {
      setTreinos(treinos.map(t => t.id === treinoEditando.id ? treinoEditando : t));
    } else if (modo === 'adicionar') {
      const novoId = Math.max(...treinos.map(t => t.id), 0) + 1;
      setTreinos([...treinos, {...treinoEditando, id: novoId}]);
    }
    setModo('visualizar');
    setTreinoEditando(null);
  };

  const excluirTreino = (id) => {
    setTreinos(treinos.filter(t => t.id !== id));
    if (treinoAtual >= treinosFiltrados.length - 1) {
      setTreinoAtual(Math.max(0, treinosFiltrados.length - 2));
    }
  };

  const iniciarNovoTreino = () => {
    setTreinoEditando({
      nome: "Novo Treino",
      nivel: "iniciante",
      parte: "corpo completo",
      duracao: 30,
      exercicios: [
        { nome: "Novo Exercício", series: 3, repeticoes: 10, descanso: 60 }
      ]
    });
    setModo('adicionar');
  };

  const adicionarExercicio = () => {
    setTreinoEditando({
      ...treinoEditando,
      exercicios: [
        ...treinoEditando.exercicios,
        { nome: "Novo Exercício", series: 3, repeticoes: 10, descanso: 60 }
      ]
    });
  };

  const removerExercicio = (index) => {
    const novosExercicios = [...treinoEditando.exercicios];
    novosExercicios.splice(index, 1);
    setTreinoEditando({...treinoEditando, exercicios: novosExercicios});
  };

  const atualizarExercicio = (index, campo, valor) => {
    const novosExercicios = [...treinoEditando.exercicios];
    novosExercicios[index] = {...novosExercicios[index], [campo]: valor};
    setTreinoEditando({...treinoEditando, exercicios: novosExercicios});
  };

  // Efeito para controlar o cronômetro
  useEffect(() => {
    if (modo === 'executar' && !pausado) {
      timerRef.current = setInterval(() => {
        setCronometro((prevTime) => prevTime - 1);
      }, 1000);
    }
    
    return () => {
      if (timerRef.current) clearInterval(timerRef.current);
    };
  }, [modo, pausado]);
  
  // Efeito para verificar o fim da contagem e avançar
  useEffect(() => {
    if (modo !== 'executar') return;
    
    if (cronometro <= 0) {
      if (timerRef.current) clearInterval(timerRef.current);
      
      if (emExercicio) {
        // Se terminou o exercício, passar para o descanso
        const exercicioAtivo = treinosFiltrados[treinoAtual].exercicios[exercicioAtual];
        setCronometro(exercicioAtivo.descanso);
        setEmExercicio(false);
      } else {
        // Se terminou o descanso
        if (serieAtual < treinosFiltrados[treinoAtual].exercicios[exercicioAtual].series) {
          // Se ainda tem séries, volta para o exercício
          setSerieAtual(serieAtual + 1);
          setEmExercicio(true);
          setCronometro(30); // Tempo padrão para exercício (30 segundos)
        } else {
          // Se terminou todas as séries deste exercício
          if (exercicioAtual < treinosFiltrados[treinoAtual].exercicios.length - 1) {
            // Passar para o próximo exercício
            setExercicioAtual(exercicioAtual + 1);
            setSerieAtual(1);
            setEmExercicio(true);
            setCronometro(30); // Tempo padrão para exercício (30 segundos)
          } else {
            // Terminou o treino completo
            setModo('visualizar');
            alert('Parabéns! Você completou o treino!');
          }
        }
      }
    }
  }, [cronometro, modo, exercicioAtual, serieAtual, emExercicio, treinoAtual, treinosFiltrados]);
  
  // Iniciar execução do treino
  const iniciarExecucao = () => {
    setModo('executar');
    setExercicioAtual(0);
    setSerieAtual(1);
    setEmExercicio(true);
    setCronometro(30); // Tempo padrão para exercício (30 segundos)
    setPausado(false);
  };
  
  // Pausar/retomar cronômetro
  const togglePause = () => {
    setPausado(!pausado);
  };
  
  // Pular para o próximo exercício/série
  const pularParaProximo = () => {
    if (emExercicio) {
      // Se está no exercício, pular para o descanso
      const exercicioAtivo = treinosFiltrados[treinoAtual].exercicios[exercicioAtual];
      setCronometro(exercicioAtivo.descanso);
      setEmExercicio(false);
    } else {
      // Se está no descanso
      if (serieAtual < treinosFiltrados[treinoAtual].exercicios[exercicioAtual].series) {
        // Se ainda tem séries, volta para o exercício
        setSerieAtual(serieAtual + 1);
        setEmExercicio(true);
        setCronometro(30); // Tempo padrão para exercício (30 segundos)
      } else {
        // Se terminou todas as séries deste exercício
        if (exercicioAtual < treinosFiltrados[treinoAtual].exercicios.length - 1) {
          // Passar para o próximo exercício
          setExercicioAtual(exercicioAtual + 1);
          setSerieAtual(1);
          setEmExercicio(true);
          setCronometro(30); // Tempo padrão para exercício (30 segundos)
        }
      }
    }
  };
  
  // Formatação do cronômetro
  const formatarTempo = (segundos) => {
    const mins = Math.floor(segundos / 60);
    const segs = segundos % 60;
    return `${mins}:${segs < 10 ? '0' + segs : segs}`;
  };

  return (
    <div className="flex flex-col min-h-screen bg-gray-100">
      {/* Cabeçalho */}
      <header className="bg-blue-600 text-white p-4 shadow-md">
        <h1 className="text-2xl font-bold text-center">CaliFit</h1>
        <p className="text-center text-sm">Seu aplicativo de treino de calistenia</p>
      </header>

      {modo === 'executar' ? (
        <div className="bg-white p-4 m-2 rounded-lg shadow flex-1 flex flex-col">
          {/* Informação do treino em execução */}
          <div className="mb-4 text-center">
            <h2 className="text-xl font-bold text-blue-700">{treinosFiltrados[treinoAtual].nome}</h2>
            <p className="text-sm text-gray-600">
              {emExercicio ? 'Exercício' : 'Descanso'}
            </p>
          </div>
          
          {/* Cronômetro grande */}
          <div className="text-center mb-6 flex-1 flex flex-col justify-center items-center">
            <div className="text-6xl font-bold mb-2">
              {formatarTempo(cronometro)}
            </div>
            
            <div className="bg-blue-100 p-4 rounded-lg mb-4 w-full max-w-md">
              <div className="w-32 h-32 mx-auto mb-2">
                {getIlustracao(treinosFiltrados[treinoAtual].exercicios[exercicioAtual].nome)}
              </div>
              <h3 className="font-bold text-xl mb-2">
                {treinosFiltrados[treinoAtual].exercicios[exercicioAtual].nome}
              </h3>
              <div className="flex justify-between text-gray-700">
                <span>Série {serieAtual} de {treinosFiltrados[treinoAtual].exercicios[exercicioAtual].series}</span>
                <span>{treinosFiltrados[treinoAtual].exercicios[exercicioAtual].repeticoes} repetições</span>
              </div>
            </div>
            
            {/* Indicador de progresso */}
            <div className="w-full mb-6">
              <div className="flex justify-between text-sm text-gray-600 mb-1">
                <span>Exercício {exercicioAtual + 1} de {treinosFiltrados[treinoAtual].exercicios.length}</span>
                <span>{emExercicio ? 'Exercício' : 'Descanso'}</span>
              </div>
              <div className="h-2 bg-gray-200 rounded-full overflow-hidden">
                <div 
                  className={`h-full ${emExercicio ? 'bg-green-500' : 'bg-orange-500'}`}
                  style={{ 
                    width: `${(exercicioAtual / treinosFiltrados[treinoAtual].exercicios.length) * 100}%` 
                  }}
                ></div>
              </div>
            </div>
            
            {/* Controles */}
            <div className="flex gap-4 justify-center">
              <button 
                onClick={togglePause} 
                className={`${pausado ? 'bg-green-600' : 'bg-orange-600'} text-white p-3 rounded-full`}
              >
                {pausado ? <Play className="w-6 h-6" /> : <Pause className="w-6 h-6" />}
              </button>
              <button 
                onClick={pularParaProximo} 
                className="bg-blue-600 text-white p-3 rounded-full"
              >
                <SkipForward className="w-6 h-6" />
              </button>
            </div>
          </div>
          
          {/* Botão para encerrar treino */}
          <button 
            onClick={() => setModo('visualizar')} 
            className="bg-gray-500 text-white py-2 px-4 rounded w-full"
          >
            Encerrar Treino
          </button>
        </div>
      ) : modo === 'visualizar' ? (
        <>
          {/* Filtros */}
          <div className="bg-white p-4 m-2 rounded-lg shadow">
            <div className="flex items-center mb-2">
              <Filter className="w-5 h-5 mr-2 text-blue-600" />
              <h2 className="text-lg font-semibold">Filtros</h2>
            </div>
            <div className="flex flex-wrap gap-2">
              <select 
                className="px-3 py-2 border rounded flex-1"
                value={filtroNivel}
                onChange={(e) => setFiltroNivel(e.target.value)}
              >
                <option value="todos">Todos os níveis</option>
                <option value="iniciante">Iniciante</option>
                <option value="intermediário">Intermediário</option>
                <option value="avançado">Avançado</option>
              </select>
              <select 
                className="px-3 py-2 border rounded flex-1"
                value={filtroParte}
                onChange={(e) => setFiltroParte(e.target.value)}
              >
                <option value="todos">Todas as partes</option>
                <option value="corpo completo">Corpo completo</option>
                <option value="superior">Superior</option>
                <option value="pernas">Pernas</option>
                <option value="core">Core</option>
              </select>
            </div>
          </div>

          {treinosFiltrados.length > 0 ? (
            <div className="bg-white p-4 m-2 rounded-lg shadow flex-1">
              {/* Navegação entre treinos */}
              <div className="flex justify-between items-center mb-4">
                <button 
                  onClick={treinoAnterior} 
                  className="bg-blue-600 text-white p-2 rounded-full"
                >
                  <ArrowLeft className="w-5 h-5" />
                </button>
                <span className="font-medium">
                  {treinoAtual + 1} de {treinosFiltrados.length}
                </span>
                <button 
                  onClick={proximoTreino} 
                  className="bg-blue-600 text-white p-2 rounded-full"
                >
                  <ArrowRight className="w-5 h-5" />
                </button>
              </div>

              {/* Exibição do treino atual */}
              <div className="mb-4">
                <h2 className="text-xl font-bold text-blue-700">{treinosFiltrados[treinoAtual].nome}</h2>
                <div className="flex flex-wrap gap-2 mt-2">
                  <div className="flex items-center text-sm bg-blue-100 px-3 py-1 rounded">
                    <User className="w-4 h-4 mr-1" />
                    <span className="capitalize">{treinosFiltrados[treinoAtual].nivel}</span>
                  </div>
                  <div className="flex items-center text-sm bg-green-100 px-3 py-1 rounded">
                    <Dumbbell className="w-4 h-4 mr-1" />
                    <span className="capitalize">{treinosFiltrados[treinoAtual].parte}</span>
                  </div>
                  <div className="flex items-center text-sm bg-orange-100 px-3 py-1 rounded">
                    <Clock className="w-4 h-4 mr-1" />
                    <span>{treinosFiltrados[treinoAtual].duracao} min</span>
                  </div>
                </div>
              </div>

              {/* Lista de exercícios */}
              <div className="space-y-3 mb-4">
                <h3 className="font-semibold text-gray-700">Exercícios:</h3>
                {treinosFiltrados[treinoAtual].exercicios.map((exercicio, idx) => (
                  <div key={idx} className="border rounded p-3 bg-gray-50">
                    <h4 className="font-medium">{exercicio.nome}</h4>
                    <div className="flex flex-wrap gap-x-4 gap-y-1 mt-1 text-sm text-gray-600">
                      <span>{exercicio.series} séries</span>
                      <span>{exercicio.repeticoes} repetições</span>
                      <span>{exercicio.descanso}s descanso</span>
                    </div>
                  </div>
                ))}
              </div>

              {/* Botões de ação */}
              <div className="flex justify-between mt-4">
                <div className="flex gap-2">
                  <button 
                    onClick={() => iniciarEdicao(treinosFiltrados[treinoAtual])} 
                    className="flex items-center bg-blue-600 text-white px-4 py-2 rounded"
                  >
                    <Edit className="w-4 h-4 mr-2" />
                    Editar
                  </button>
                  <button 
                    onClick={() => excluirTreino(treinosFiltrados[treinoAtual].id)} 
                    className="flex items-center bg-red-600 text-white px-4 py-2 rounded"
                  >
                    <Trash2 className="w-4 h-4 mr-2" />
                    Excluir
                  </button>
                </div>
                <button 
                  onClick={iniciarExecucao} 
                  className="flex items-center bg-green-600 text-white px-6 py-2 rounded"
                >
                  <Play className="w-4 h-4 mr-2" />
                  Iniciar Treino
                </button>
              </div>
            </div>
          ) : (
            <div className="bg-white p-4 m-2 rounded-lg shadow text-center">
              <p className="text-gray-500 mb-2">Nenhum treino encontrado com os filtros selecionados.</p>
            </div>
          )}

          {/* Botão para adicionar novo treino */}
          <div className="flex justify-center m-4">
            <button 
              onClick={iniciarNovoTreino} 
              className="flex items-center bg-green-600 text-white px-6 py-3 rounded-full shadow-lg"
            >
              <Plus className="w-5 h-5 mr-2" />
              Novo Treino
            </button>
          </div>
        </>
      ) : (
        // Modo de edição ou adição
        <div className="bg-white p-4 m-2 rounded-lg shadow flex-1">
          <div className="mb-4 flex justify-between items-center">
            <h2 className="text-xl font-bold text-blue-700">
              {modo === 'editar' ? 'Editar Treino' : 'Novo Treino'}
            </h2>
            <button 
              onClick={cancelarEdicao}
              className="text-gray-500"
            >
              <X className="w-6 h-6" />
            </button>
          </div>

          {/* Formulário de edição */}
          <div className="space-y-4">
            <div>
              <label className="block text-sm font-medium text-gray-700 mb-1">Nome do Treino</label>
              <input 
                type="text" 
                value={treinoEditando.nome} 
                onChange={(e) => setTreinoEditando({...treinoEditando, nome: e.target.value})}
                className="w-full px-3 py-2 border rounded"
              />
            </div>

            <div className="grid grid-cols-2 gap-3">
              <div>
                <label className="block text-sm font-medium text-gray-700 mb-1">Nível</label>
                <select 
                  value={treinoEditando.nivel}
                  onChange={(e) => setTreinoEditando({...treinoEditando, nivel: e.target.value})}
                  className="w-full px-3 py-2 border rounded"
                >
                  <option value="iniciante">Iniciante</option>
                  <option value="intermediário">Intermediário</option>
                  <option value="avançado">Avançado</option>
                </select>
              </div>
              <div>
                <label className="block text-sm font-medium text-gray-700 mb-1">Parte do Corpo</label>
                <select 
                  value={treinoEditando.parte}
                  onChange={(e) => setTreinoEditando({...treinoEditando, parte: e.target.value})}
                  className="w-full px-3 py-2 border rounded"
                >
                  <option value="corpo completo">Corpo completo</option>
                  <option value="superior">Superior</option>
                  <option value="pernas">Pernas</option>
                  <option value="core">Core</option>
                </select>
              </div>
            </div>

            <div>
              <label className="block text-sm font-medium text-gray-700 mb-1">Duração (minutos)</label>
              <input 
                type="number" 
                value={treinoEditando.duracao} 
                onChange={(e) => setTreinoEditando({...treinoEditando, duracao: parseInt(e.target.value)})}
                className="w-full px-3 py-2 border rounded"
              />
            </div>

            <div>
              <div className="flex justify-between items-center mb-2">
                <label className="block text-sm font-medium text-gray-700">Exercícios</label>
                <button 
                  onClick={adicionarExercicio}
                  className="flex items-center text-sm text-blue-600"
                >
                  <Plus className="w-4 h-4 mr-1" />
                  Adicionar
                </button>
              </div>

              {treinoEditando.exercicios.map((exercicio, idx) => (
                <div key={idx} className="border rounded p-3 bg-gray-50 mb-3">
                  <div className="flex justify-between items-center mb-2">
                    <div className="flex items-center flex-1">
                      <input 
                        type="text" 
                        value={exercicio.nome} 
                        onChange={(e) => atualizarExercicio(idx, 'nome', e.target.value)}
                        className="flex-1 px-2 py-1 border rounded mr-2"
                        placeholder="Nome do exercício"
                      />
                    </div>
                    <button 
                      onClick={() => removerExercicio(idx)}
                      className="text-red-500"
                    >
                      <Trash2 className="w-4 h-4" />
                    </button>
                  </div>
                  
                  <div className="flex mb-2">
                    <div className="w-20 h-20 mr-3 flex-shrink-0 bg-blue-50 rounded border">
                      {getIlustracao(exercicio.nome)}
                    </div>
                    <div className="grid grid-cols-3 gap-2 flex-1">
                      <div>
                        <label className="block text-xs text-gray-600">Séries</label>
                        <input 
                          type="number" 
                          value={exercicio.series} 
                          onChange={(e) => atualizarExercicio(idx, 'series', parseInt(e.target.value))}
                          className="w-full px-2 py-1 border rounded text-sm"
                        />
                      </div>
                      <div>
                        <label className="block text-xs text-gray-600">Repetições</label>
                        <input 
                          type="text" 
                          value={exercicio.repeticoes} 
                          onChange={(e) => atualizarExercicio(idx, 'repeticoes', e.target.value)}
                          className="w-full px-2 py-1 border rounded text-sm"
                        />
                      </div>
                      <div>
                        <label className="block text-xs text-gray-600">Descanso (s)</label>
                        <input 
                          type="number" 
                          value={exercicio.descanso} 
                          onChange={(e) => atualizarExercicio(idx, 'descanso', parseInt(e.target.value))}
                          className="w-full px-2 py-1 border rounded text-sm"
                        />
                      </div>
                    </div>
                  </div>
                </div>
              ))}
            </div>

            <div className="flex justify-end">
              <button 
                onClick={salvarTreino}
                className="flex items-center bg-green-600 text-white px-4 py-2 rounded"
              >
                <Save className="w-4 h-4 mr-2" />
                Salvar
              </button>
            </div>
          </div>
        </div>
      )}

      {exercicioDetalhes && (
        <div className="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center z-10">
          <div className="bg-white p-4 rounded-lg shadow-lg max-w-md w-full mx-4">
            <div className="flex justify-between items-center mb-4">
              <h3 className="text-xl font-bold text-blue-700">{exercicioDetalhes.nome}</h3>
              <button 
                onClick={() => setExercicioDetalhes(null)}
                className="text-gray-500"
              >
                <X className="w-5 h-5" />
              </button>
            </div>
            
            <div className="w-48 h-48 mx-auto mb-4">
              {getIlustracao(exercicioDetalhes.nome)}
            </div>
            
            <div className="space-y-2 mb-4">
              <div className="flex justify-between">
                <span className="font-medium">Séries:</span>
                <span>{exercicioDetalhes.series}</span>
              </div>
              <div className="flex justify-between">
                <span className="font-medium">Repetições:</span>
                <span>{exercicioDetalhes.repeticoes}</span>
              </div>
              <div className="flex justify-between">
                <span className="font-medium">Descanso:</span>
                <span>{exercicioDetalhes.descanso} segundos</span>
              </div>
            </div>
            
            <div className="mt-4">
              <button 
                onClick={() => setExercicioDetalhes(null)}
                className="w-full bg-blue-600 text-white py-2 rounded"
              >
                Fechar
              </button>
            </div>
          </div>
        </div>
      )}

      {/* Rodapé */}
      <footer className="bg-gray-800 text-white p-4 text-center text-sm">
        <p>© 2025 CaliFit - Aplicativo de Treino de Calistenia</p>
      </footer>
    </div>
  );
}
