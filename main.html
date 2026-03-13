import React, { useState, useEffect, useRef } from 'react';
import * as THREE from 'three';
import { 
  Crosshair, Target, Zap, ArrowLeft, Settings, Shield, Sword, 
  User, ChevronRight, LayoutGrid, Bomb, Skull, RotateCcw, MousePointer2, Trophy, Frown
} from 'lucide-react';

// --- WEAPON DATA (Wiki Accurate) ---
const WEAPONS = {
  PRIMARY: [
    { id: 'ar', name: 'Assault Rifle', damage: 18, headshot: 1.5, rps: 11, color: '#3b82f6', desc: 'Fast fire rate, reliable.' },
    { id: 'sniper', name: 'Sniper', damage: 85, headshot: 3.0, rps: 0.7, color: '#111111', desc: '1-shot headshot (255 DMG).' },
    { id: 'shotgun', name: 'Shotgun', damage: 10, headshot: 1.2, pellets: 8, color: '#450a0a', desc: 'Devastating up close.' }
  ],
  SECONDARY: [{ id: 'revolver', name: 'Revolver', damage: 45, headshot: 1.5, desc: 'High damage sidearm.' }],
  MELEE: [{ id: 'knife', name: 'Knife', damage: 55, desc: 'Close combat blade.' }],
  UTILITY: [{ id: 'grenade', name: 'Grenade', damage: 120, desc: 'Explosive throw.' }]
};

const GAME_MODES = [
  { id: '1v1', label: '1v1 DUEL', bots: 1, allies: 0 },
  { id: '2v2', label: '2v2 ARENA', bots: 2, allies: 1 },
  { id: '3v3', label: '3v3 SKIRMISH', bots: 3, allies: 2 },
  { id: '4v4', label: '4v4 BATTLE', bots: 4, allies: 3 },
  { id: '5v5', label: '5v5 WAR', bots: 5, allies: 4 }
];

export default function App() {
  const [screen, setScreen] = useState('menu'); // menu, mode_select, loadout, game, match_over
  const [round, setRound] = useState(1);
  const [scores, setScores] = useState({ player: 0, enemy: 0 });
  const [matchStatus, setMatchStatus] = useState(''); // '', 'ROUND WON', 'ROUND LOST', 'MATCH WON', 'MATCH LOST'
  const [mode, setMode] = useState(GAME_MODES[0]);
  const [loadout, setLoadout] = useState(WEAPONS.PRIMARY[0]);
  const [stats, setStats] = useState({ hp: 100, kills: 0 });
  const [isMobile, setIsMobile] = useState(false);
  const [isAiming, setIsAiming] = useState(false);
  const [isLocked, setIsLocked] = useState(false);

  const mountRef = useRef(null);
  const gameActive = useRef(false);
  const gameState = useRef({ isDead: false, roundEnded: false, spectating: null });
  const moveJoy = useRef({ x: 0, y: 0 });
  const lastTouch = useRef({ x: 0, y: 0 }); // For mobile looking

  // Detect mobile
  useEffect(() => {
    setIsMobile('ontouchstart' in window || navigator.maxTouchPoints > 0);
  }, []);

  // --- CORE GAME ENGINE ---
  useEffect(() => {
    if (screen !== 'game' || !mountRef.current) return;

    // Reset loop state for new round
    gameState.current = { isDead: false, roundEnded: false, spectating: null };
    setStats(s => ({ ...s, hp: 100 }));
    setMatchStatus('');

    // 1. SCENE SETUP
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x0a0a0c);
    scene.fog = new THREE.Fog(0x0a0a0c, 10, 150);

    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    mountRef.current.appendChild(renderer.domElement);

    // 2. LIGHTING
    scene.add(new THREE.AmbientLight(0xffffff, 0.4));
    const sun = new THREE.DirectionalLight(0xffffff, 0.8);
    sun.position.set(50, 100, 50);
    scene.add(sun);

    // 3. ARENA GENERATION & WALLS
    const walls = [];
    const floor = new THREE.Mesh(
      new THREE.PlaneGeometry(200, 200), 
      new THREE.MeshPhongMaterial({ color: 0x111111, shininess: 10 })
    );
    floor.rotation.x = -Math.PI / 2;
    scene.add(floor);

    const wallMat = new THREE.MeshPhongMaterial({ color: 0xffffff }); // Rivals White Walls
    const createWall = (w, h, d, x, z) => {
      const b = new THREE.Mesh(new THREE.BoxGeometry(w, h, d), wallMat);
      b.position.set(x, h/2, z);
      b.userData = { isWall: true };
      scene.add(b);
      walls.push(b);
      return b;
    };
    
    // Bounds
    createWall(200, 20, 2, 0, 100);
    createWall(200, 20, 2, 0, -100);
    createWall(2, 20, 200, 100, 0);
    createWall(2, 20, 200, -100, 0);

    // Random Cover/Blocks
    for(let i=0; i<30; i++) {
      createWall(4+Math.random()*8, 10, 4+Math.random()*8, Math.random()*160-80, Math.random()*160-80);
    }

    // Collision Helper (AABB)
    const canMoveTo = (pos, radius = 0.6) => {
      for(let w of walls) {
         const bx = w.position.x;
         const bz = w.position.z;
         const bw = w.geometry.parameters.width / 2;
         const bd = w.geometry.parameters.depth / 2;
         if (pos.x > bx - bw - radius && pos.x < bx + bw + radius && 
             pos.z > bz - bd - radius && pos.z < bz + bd + radius) {
             return false;
         }
      }
      return true;
    };

    // 4. ENTITIES (NPCs)
    let entities = [];
    const spawnEntity = (team, x, z) => {
      const g = new THREE.Group();
      
      const body = new THREE.Mesh(
        new THREE.BoxGeometry(1.2, 2, 0.8), 
        new THREE.MeshPhongMaterial({ color: team === 'red' ? 0xff3333 : 0x33ff33 })
      );
      body.position.y = 1;
      g.add(body);
      
      const head = new THREE.Mesh(
        new THREE.BoxGeometry(0.8, 0.8, 0.8), 
        new THREE.MeshPhongMaterial({ color: 0xffdbac })
      );
      head.position.y = 2.4;
      head.name = "HEAD";
      g.add(head);
      
      const gun = new THREE.Mesh(new THREE.BoxGeometry(0.2, 0.3, 1), new THREE.MeshPhongMaterial({ color: 0x222222 }));
      gun.position.set(0.7, 1.2, 0.5);
      g.add(gun);

      g.position.set(x, 0, z);
      g.userData = { isEntity: true };
      scene.add(g);
      
      entities.push({ 
          mesh: g, team, hp: 100, lastShot: Date.now() + Math.random()*2000, 
          isDead: false, targetPos: new THREE.Vector3(x, 0, z) 
      });
    };

    for(let i=0; i<mode.bots; i++) spawnEntity('red', Math.random()*60-30, -50 - Math.random()*20);
    for(let i=0; i<mode.allies; i++) spawnEntity('green', Math.random()*40-20, 40 + Math.random()*10);

    // 5. PLAYER VARIABLES & CONTROLS
    camera.position.set(0, 1.8, 80);
    let pitch = 0, yaw = 0;
    const keys = {};

    const onKeyDown = (e) => keys[e.code] = true;
    const onKeyUp = (e) => keys[e.code] = false;

    const onMouseMove = (e) => {
      if (document.pointerLockElement === renderer.domElement && !gameState.current.isDead) {
        yaw -= e.movementX * 0.002;
        pitch -= e.movementY * 0.002;
        pitch = Math.max(-1.5, Math.min(1.5, pitch));
        if (!isLocked) setIsLocked(true);
      } else {
        setIsLocked(false);
      }
    };

    window.handleMobileLookStart = (e) => { lastTouch.current = { x: e.touches[0].clientX, y: e.touches[0].clientY }; };
    window.handleMobileLookMove = (e) => {
      if(gameState.current.isDead) return;
      const touch = e.touches[0];
      yaw -= (touch.clientX - lastTouch.current.x) * 0.004;
      pitch -= (touch.clientY - lastTouch.current.y) * 0.004;
      pitch = Math.max(-1.5, Math.min(1.5, pitch));
      lastTouch.current = { x: touch.clientX, y: touch.clientY };
    };

    // Smooth Tracers System
    const tracers = [];
    const addTracer = (start, end, isEnemy) => {
         const geo = new THREE.BufferGeometry().setFromPoints([start, start]);
         const mat = new THREE.LineBasicMaterial({ color: isEnemy ? 0xff4444 : 0xffffaa, transparent: true, opacity: 0.8 });
         const line = new THREE.Line(geo, mat);
         scene.add(line);
         tracers.push({ line, start, end, progress: 0 });
    };

    // Firing Logic (Raycast checks Walls + NPCs)
    const fire = () => {
      if (gameState.current.isDead || gameState.current.roundEnded) return;

      const ray = new THREE.Raycaster();
      ray.setFromCamera(new THREE.Vector2(0, 0), camera); // Perfect center aim
      
      const targets = [...walls, ...entities.map(e => e.mesh)];
      const hits = ray.intersectObjects(targets, true);

      pitch += 0.04; // Visual Recoil

      // Tracer Origin
      const startPos = camera.position.clone().add(new THREE.Vector3(0.3, -0.3, -1).applyQuaternion(camera.quaternion));
      
      if (hits.length > 0) {
        const hit = hits[0];
        addTracer(startPos, hit.point, false);

        if (!hit.object.userData.isWall && hit.object.parent) {
            // Hit Entity
            const parentMesh = hit.object.parent;
            const targetEnt = entities.find(e => e.mesh === parentMesh);
            
            if (targetEnt && !targetEnt.isDead) {
                const isHead = hit.object.name === "HEAD";
                let damage = loadout.damage;
                if (isHead) damage *= loadout.headshot;

                const spark = new THREE.Mesh(new THREE.SphereGeometry(0.2), new THREE.MeshBasicMaterial({color: isHead ? 0xff0000 : 0xffff00}));
                spark.position.copy(hit.point);
                scene.add(spark);
                setTimeout(() => scene.remove(spark), 100);

                targetEnt.hp -= damage;
                
                // Death check
                if (targetEnt.hp <= 0) {
                    targetEnt.isDead = true;
                    scene.remove(targetEnt.mesh);
                    if (targetEnt.team === 'red') setStats(s => ({ ...s, kills: s.kills + 1 }));
                }
            }
        }
      } else {
         addTracer(startPos, ray.ray.at(100, new THREE.Vector3()), false);
      }
    };

    window.addEventListener('keydown', onKeyDown);
    window.addEventListener('keyup', onKeyUp);
    window.addEventListener('mousemove', onMouseMove);
    window.addEventListener('mousedown', (e) => {
        if(e.button === 0 && document.pointerLockElement) fire();
        if(e.button === 2) setIsAiming(true);
    });
    window.addEventListener('mouseup', (e) => { if(e.button === 2) setIsAiming(false); });
    window.mobileFire = fire;
    window.requestLock = () => renderer.domElement.requestPointerLock();

    // Round End Handler
    const handleRoundEnd = (winnerTeam) => {
        gameState.current.roundEnded = true;
        setMatchStatus(winnerTeam === 'player' ? 'ROUND WON' : 'ROUND LOST');
        
        setTimeout(() => {
            setScores(prev => {
                const updated = { ...prev, [winnerTeam]: prev[winnerTeam] + 1 };
                if (updated.player >= 4 || updated.enemy >= 4) {
                    setMatchStatus(updated.player >= 4 ? 'MATCH WON' : 'MATCH LOST');
                    setTimeout(() => setScreen('match_over'), 2000);
                } else {
                    setRound(r => r + 1); // Triggers remount for next round
                }
                return updated;
            });
        }, 2500);
    };

    // 6. ANIMATION LOOP
    const animate = () => {
      if (!gameActive.current) return;
      requestAnimationFrame(animate);

      // Alive teams check
      const aliveRed = entities.filter(e => e.team === 'red' && !e.isDead);
      const aliveGreen = entities.filter(e => e.team === 'green' && !e.isDead);

      if (!gameState.current.roundEnded) {
         if (aliveRed.length === 0) handleRoundEnd('player');
         else if (gameState.current.isDead && aliveGreen.length === 0) handleRoundEnd('enemy');
      }

      // PLAYER CAMERA / MOVEMENT
      if (!gameState.current.isDead) {
          camera.rotation.set(pitch, yaw, 0, 'YXZ');
          
          const xInput = (keys['KeyD'] ? 1 : 0) - (keys['KeyA'] ? 1 : 0) + moveJoy.current.x;
          const zInput = (keys['KeyS'] ? 1 : 0) - (keys['KeyW'] ? 1 : 0) + moveJoy.current.y;
          const speed = keys['ShiftLeft'] ? 0.35 : 0.15;
          
          if (xInput !== 0 || zInput !== 0) {
            const inputVec = new THREE.Vector3(xInput, 0, zInput).normalize();
            inputVec.applyAxisAngle(new THREE.Vector3(0, 1, 0), yaw);

            const nextX = camera.position.clone(); nextX.x += inputVec.x * speed;
            const nextZ = camera.position.clone(); nextZ.z += inputVec.z * speed;
            
            if (Math.abs(inputVec.x) > 0 && canMoveTo(nextX)) camera.position.x = nextX.x;
            if (Math.abs(inputVec.z) > 0 && canMoveTo(nextZ)) camera.position.z = nextZ.z;
          }
          camera.position.y = 1.8;

          // Smooth Aiming FOV
          camera.fov = THREE.MathUtils.lerp(camera.fov, isAiming ? 40 : 75, 0.15);
          camera.updateProjectionMatrix();

      } else {
          // SPECTATOR MODE
          if (!gameState.current.spectating || gameState.current.spectating.isDead) {
             const available = entities.filter(e => !e.isDead);
             gameState.current.spectating = available.length > 0 ? available[0] : null;
          }
          
          if (gameState.current.spectating) {
              const targetPos = gameState.current.spectating.mesh.position;
              const time = Date.now() * 0.001;
              const offset = new THREE.Vector3(Math.cos(time)*6, 4, Math.sin(time)*6);
              camera.position.lerp(targetPos.clone().add(offset), 0.05);
              camera.lookAt(targetPos.clone().add(new THREE.Vector3(0, 1.5, 0)));
          } else {
              // Everyone dead, fall to ground
              camera.position.y = THREE.MathUtils.lerp(camera.position.y, 0.2, 0.05);
              camera.rotation.z = THREE.MathUtils.lerp(camera.rotation.z, Math.PI/4, 0.02);
          }
      }

      // TRACERS ANIMATION
      for (let i = tracers.length - 1; i >= 0; i--) {
          const t = tracers[i];
          t.progress += 0.2;
          const curStart = t.start.clone().lerp(t.end, Math.min(1, t.progress - 0.2));
          const curEnd = t.start.clone().lerp(t.end, Math.min(1, t.progress));
          t.line.geometry.setFromPoints([curStart, curEnd]);
          if (t.progress >= 1.2) {
              scene.remove(t.line);
              tracers.splice(i, 1);
          }
      }

      // NPC AI & MOVEMENT
      entities.forEach(ent => {
        if (ent.isDead) return;

        // Determine target
        let targetPos = null;
        if (ent.team === 'red') targetPos = gameState.current.isDead ? (aliveGreen[0]?.mesh.position || null) : camera.position;
        else targetPos = aliveRed[0]?.mesh.position || null; // Green attacks red

        if (targetPos) {
            const dist = ent.mesh.position.distanceTo(targetPos);
            ent.mesh.lookAt(targetPos.x, 1, targetPos.z);
            
            // Move logic
            const dir = targetPos.clone().sub(ent.mesh.position).normalize();
            ent.targetPos.copy(ent.mesh.position);

            if (dist > 15) ent.targetPos.add(dir.multiplyScalar(0.08));
            else if (dist < 8) ent.targetPos.add(dir.multiplyScalar(-0.05));

            // Apply AABB Collision to NPC
            const nX = new THREE.Vector3(ent.targetPos.x, 0, ent.mesh.position.z);
            const nZ = new THREE.Vector3(ent.mesh.position.x, 0, ent.targetPos.z);
            if (!canMoveTo(nX, 0.6)) ent.targetPos.x = ent.mesh.position.x;
            if (!canMoveTo(nZ, 0.6)) ent.targetPos.z = ent.mesh.position.z;

            // Smooth Interpolated Movement
            ent.mesh.position.lerp(ent.targetPos, 0.2);

            // Shoot Logic
            const now = Date.now();
            if (dist < 60 && now - ent.lastShot > 1800) {
                ent.lastShot = now;

                const gunOffset = new THREE.Vector3(0, 1.5, 0);
                const start = ent.mesh.position.clone().add(gunOffset);
                const end = targetPos.clone().add(gunOffset);
                addTracer(start, end, ent.team === 'red');

                // Visual Muzzle Flash
                const flash = new THREE.Mesh(new THREE.SphereGeometry(0.3), new THREE.MeshBasicMaterial({color: 0xffaa00}));
                flash.position.copy(start);
                scene.add(flash);
                setTimeout(() => scene.remove(flash), 100);

                // Accuracy checks via raycast to ensure wall cover works
                const nRay = new THREE.Raycaster(start, end.clone().sub(start).normalize());
                const nHits = nRay.intersectObjects(walls);
                
                if (nHits.length === 0 || nHits[0].distance > dist) {
                     // Wall didn't block it
                     if (ent.team === 'red' && targetPos === camera.position && Math.random() > 0.6) {
                         setStats(s => {
                             const nHp = s.hp - 15;
                             if (nHp <= 0 && !gameState.current.isDead) {
                                 gameState.current.isDead = true;
                                 document.exitPointerLock?.();
                             }
                             return { ...s, hp: nHp };
                         });
                     } else if (targetPos !== camera.position && Math.random() > 0.5) {
                         // Bot hits Bot
                         const targetEnt = entities.find(e => e.mesh.position === targetPos);
                         if (targetEnt) {
                             targetEnt.hp -= 20;
                             if (targetEnt.hp <= 0) {
                                targetEnt.isDead = true;
                                scene.remove(targetEnt.mesh);
                             }
                         }
                     }
                }
            }
        }
      });

      renderer.render(scene, camera);
    };

    gameActive.current = true;
    animate();

    return () => {
      gameActive.current = false;
      window.removeEventListener('keydown', onKeyDown);
      window.removeEventListener('keyup', onKeyUp);
      window.removeEventListener('mousemove', onMouseMove);
      if (mountRef.current) mountRef.current.innerHTML = '';
    };
  }, [screen, round, mode, loadout]); // Scene fully rebuilds on round change

  const initMatch = (selectedMode) => {
    setMode(selectedMode);
    setScores({ player: 0, enemy: 0 });
    setRound(1);
    setStats({ hp: 100, kills: 0 });
    setScreen('game');
  };

  // --- UI RENDER ---
  return (
    <div className="w-full h-screen bg-black text-white font-sans overflow-hidden select-none touch-none">
      
      {/* --- MATCH OVER SCREEN --- */}
      {screen === 'match_over' && (
        <div className="absolute inset-0 z-[100] bg-black/95 backdrop-blur-2xl flex flex-col items-center justify-center p-10 animate-in fade-in duration-500">
          {scores.player >= 4 ? <Trophy size={140} className="text-yellow-500 mb-6 drop-shadow-[0_0_30px_rgba(234,179,8,0.5)]" /> : <Frown size={140} className="text-red-600 mb-6 drop-shadow-[0_0_30px_rgba(220,38,38,0.5)]" />}
          <h1 className={`text-7xl sm:text-9xl font-black italic tracking-tighter mb-2 drop-shadow-2xl ${scores.player >= 4 ? 'text-white' : 'text-red-600'}`}>
             {scores.player >= 4 ? 'VICTORY' : 'DEFEAT'}
          </h1>
          <p className="text-zinc-400 font-bold uppercase tracking-[0.3em] mb-10 text-center text-xl">
             FINAL SCORE: {scores.player} - {scores.enemy}
          </p>
          
          <button onClick={() => setScreen('menu')} className="bg-white text-black px-12 py-5 font-black italic text-2xl flex items-center justify-center gap-3 hover:scale-105 transition-transform skew-x-[-10deg]">
             <RotateCcw size={28}/> RETURN TO MENU
          </button>
        </div>
      )}

      {/* --- GAME HUD --- */}
      {screen === 'game' && (
        <div className="absolute inset-0 z-50 pointer-events-none">
          
          {/* Round End Overlay */}
          {matchStatus && (
             <div className="absolute inset-0 bg-black/60 flex items-center justify-center z-50 animate-in zoom-in-90 duration-300">
                <h2 className="text-6xl sm:text-8xl font-black italic text-white tracking-widest drop-shadow-[0_0_20px_rgba(255,255,255,0.8)] uppercase">{matchStatus}</h2>
             </div>
          )}

          {/* Match Score UI (Top Left) */}
          <div className="absolute top-6 left-6 flex gap-4">
             <div className="bg-blue-600/80 px-6 py-3 rounded-lg border-2 border-white/20 skew-x-[-10deg] flex flex-col items-center min-w-[100px] shadow-xl">
                 <span className="text-[10px] font-bold uppercase tracking-widest text-white/80">Ally Team</span>
                 <span className="text-4xl font-black">{scores.player}</span>
             </div>
             <div className="bg-red-600/80 px-6 py-3 rounded-lg border-2 border-white/20 skew-x-[-10deg] flex flex-col items-center min-w-[100px] shadow-xl">
                 <span className="text-[10px] font-bold uppercase tracking-widest text-white/80">Enemy Team</span>
                 <span className="text-4xl font-black">{scores.enemy}</span>
             </div>
          </div>

          {/* Match Point Warning */}
          {(scores.player === 3 || scores.enemy === 3) && !matchStatus && (
              <div className="absolute top-24 left-6 text-yellow-500 font-black italic tracking-widest animate-pulse border-l-4 border-yellow-500 pl-3">
                  MATCH POINT
              </div>
          )}

          {/* Lock Mouse Prompt (PC) */}
          {!isMobile && !isLocked && !matchStatus && !gameState.current.isDead && (
             <div className="absolute inset-0 bg-black/50 backdrop-blur-sm flex items-center justify-center pointer-events-auto">
                <button onClick={() => window.requestLock()} className="bg-red-600 px-10 py-6 rounded-2xl font-black italic text-3xl flex items-center gap-4 hover:scale-105 transition-all shadow-2xl shadow-red-600/50">
                   <MousePointer2 size={40}/> CLICK TO LOCK VIEW
                </button>
             </div>
          )}

          {/* Fixed CSS Mini Crosshair (Replaces old dynamic box) */}
          {!gameState.current.isDead && (
             <div className="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 flex items-center justify-center mix-blend-difference">
                 <div className={`absolute w-5 h-0.5 bg-white transition-transform duration-200 ${isAiming ? 'scale-50' : ''}`}></div>
                 <div className={`absolute w-0.5 h-5 bg-white transition-transform duration-200 ${isAiming ? 'scale-50' : ''}`}></div>
             </div>
          )}

          {/* Spectator Indicator */}
          {gameState.current.isDead && !matchStatus && (
             <div className="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 mt-32 bg-black/80 text-white px-6 py-2 rounded-full border border-white/20 font-bold tracking-widest uppercase animate-pulse">
                Spectating (Dead)
             </div>
          )}

          {/* Health Bar */}
          {!gameState.current.isDead && (
            <div className="absolute bottom-10 left-10 p-6 bg-black/80 border-l-4 border-red-600 skew-x-[-10deg]">
              <div className="text-[10px] font-black text-red-500 uppercase tracking-widest">Health</div>
              <div className="w-48 sm:w-64 h-3 bg-zinc-800 mt-1"><div className="h-full bg-red-600 transition-all duration-300" style={{width: `${Math.max(0, stats.hp)}%`}}></div></div>
              <div className="mt-2 text-xs font-bold text-white flex items-center gap-2">
                 ROUND {round} (FIRST TO 4)
              </div>
            </div>
          )}

          {/* Score & Mode HUD */}
          <div className="absolute top-10 left-1/2 -translate-x-1/2 flex items-center gap-10 bg-zinc-900/90 px-10 py-3 rounded-full border border-white/10 shadow-xl">
            <div className="text-center"><div className="text-[10px] font-bold text-zinc-500 tracking-widest">MY KILLS</div><div className="text-3xl font-black text-red-600 leading-none">{stats.kills}</div></div>
            <div className="w-px h-8 bg-white/10"></div>
            <div className="text-center"><div className="text-[10px] font-bold text-zinc-500 tracking-widest">MODE</div><div className="text-xl font-black text-white pt-1">{mode.label}</div></div>
          </div>

          {/* Weapon Name */}
          {!gameState.current.isDead && (
            <div className="absolute bottom-10 right-10 text-right uppercase italic font-black">
               <div className="text-5xl leading-none">{loadout.name}</div>
               <div className="text-red-500 text-lg">∞ AMMO</div>
            </div>
          )}

          {/* Mobile Overlay */}
          {isMobile && !gameState.current.isDead && (
            <>
              {/* Look Area (Right Half) */}
              <div className="absolute top-0 right-0 w-1/2 h-full z-40 pointer-events-auto touch-none"
                   onTouchStart={(e) => window.handleMobileLookStart(e)}
                   onTouchMove={(e) => window.handleMobileLookMove(e)}
              />

              {/* Move Joystick */}
              <div className="absolute bottom-12 left-12 w-32 h-32 bg-white/5 rounded-full border border-white/10 z-50 pointer-events-auto touch-none"
                onTouchMove={(e) => {
                  const t = e.touches[0]; const r = e.target.getBoundingClientRect();
                  moveJoy.current = { x: (t.clientX - (r.left + 64))/64, y: (t.clientY - (r.top + 64))/64 };
                }}
                onTouchEnd={() => moveJoy.current = {x:0, y:0}}
              />

              {/* Action Buttons */}
              <div className="absolute right-12 bottom-32 flex flex-col gap-4 z-50 pointer-events-auto">
                <button onTouchStart={() => window.mobileFire()} className="w-20 h-20 bg-red-600 rounded-full flex items-center justify-center border-4 border-white/20 active:scale-90 shadow-2xl shadow-red-900"><Zap fill="white" size={32}/></button>
                <button onTouchStart={() => setIsAiming(true)} onTouchEnd={() => setIsAiming(false)} className="w-16 h-16 bg-zinc-800 rounded-full flex items-center justify-center border-2 border-white/10 active:scale-90"><Target size={24}/></button>
              </div>
            </>
          )}

          {/* Quit to Menu */}
          <button onClick={() => { setScreen('menu'); document.exitPointerLock?.(); }} className="absolute top-6 right-6 pointer-events-auto bg-zinc-900 border border-white/10 px-4 py-2 text-xs font-bold uppercase rounded hover:bg-red-600 transition-colors">QUIT</button>
        </div>
      )}

      {/* --- MENU / LOBBY --- */}
      {screen !== 'game' && screen !== 'match_over' && (
        <div className="w-full h-full bg-[radial-gradient(circle_at_center,_#2a0a0a_0%,_#000000_100%)] flex flex-col">
          
          {/* Header */}
          <div className="p-6 flex justify-between items-center bg-black/50 backdrop-blur-md border-b border-white/5">
            <div className="flex items-center gap-4">
               <div className="w-12 h-12 bg-red-600 flex items-center justify-center font-black text-2xl italic rounded shadow-lg shadow-red-600/50">R</div>
               <div>
                  <h1 className="text-2xl font-black italic tracking-tighter leading-none">RIVALS</h1>
                  <p className="text-[10px] text-red-500 font-bold uppercase tracking-widest">Pro Arena</p>
               </div>
            </div>
            <div className="bg-zinc-900 px-6 py-2 rounded-full border border-white/10 text-xs font-bold text-yellow-500">12,500 CR</div>
          </div>

          <div className="flex-1 flex flex-col items-center justify-center p-6 overflow-y-auto">
            
            {/* MAIN MENU */}
            {screen === 'menu' && (
              <div className="flex flex-col items-center gap-12 w-full max-w-sm">
                 <div className="text-center w-full">
                   <h2 className="text-7xl sm:text-9xl font-black italic tracking-tighter text-white drop-shadow-2xl select-none">RIVALS</h2>
                   <div className="h-2 w-full bg-red-600 mt-[-10px] sm:mt-[-15px] skew-x-[-15deg]"></div>
                 </div>
                 
                 <div className="flex flex-col gap-4 w-full">
                   <button onClick={() => setScreen('mode_select')} className="w-full bg-red-600 py-6 text-3xl font-black italic skew-x-[-10deg] border-b-8 border-red-900 hover:bg-white hover:text-red-600 transition-all active:translate-y-2 active:border-b-0 shadow-2xl">PLAY</button>
                   <button onClick={() => setScreen('loadout')} className="w-full bg-zinc-900 py-4 font-bold text-xl skew-x-[-10deg] border-b-4 border-black hover:bg-zinc-800 transition-colors">ARMORY</button>
                 </div>
              </div>
            )}

            {/* MODE SELECTOR */}
            {screen === 'mode_select' && (
              <div className="w-full max-w-6xl">
                <h2 className="text-4xl font-black italic mb-8 text-center sm:text-left">SELECT MODE (FIRST TO 4)</h2>
                <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-5 gap-6">
                  {GAME_MODES.map(m => (
                    <div key={m.id} onClick={() => initMatch(m)} 
                      className="group bg-zinc-900/50 border-2 border-white/5 hover:border-red-600 p-8 rounded-2xl cursor-pointer transition-all hover:-translate-y-2 relative overflow-hidden">
                      <div className="absolute inset-0 bg-red-600/0 group-hover:bg-red-600/10 transition-colors"></div>
                      <LayoutGrid size={48} className="text-red-600 mb-6 group-hover:scale-110 transition-transform" />
                      <h3 className="text-2xl font-black italic">{m.label}</h3>
                      <p className="text-zinc-500 text-xs font-bold mt-2">VS {m.bots} BOTS</p>
                    </div>
                  ))}
                </div>
                <button onClick={() => setScreen('menu')} className="mt-12 flex items-center gap-2 text-zinc-500 hover:text-white font-bold transition-colors uppercase"><ArrowLeft size={20}/> Back to Menu</button>
              </div>
            )}

            {/* ARMORY / LOADOUT */}
            {screen === 'loadout' && (
              <div className="w-full max-w-5xl grid grid-cols-1 lg:grid-cols-2 gap-10">
                 <div className="space-y-4">
                    <h2 className="text-4xl font-black italic mb-6">PRIMARY WEAPON</h2>
                    {WEAPONS.PRIMARY.map(w => (
                       <div key={w.id} onClick={() => setLoadout(w)}
                         className={`p-6 rounded-xl border-2 flex flex-col justify-center cursor-pointer transition-all ${loadout.id === w.id ? 'bg-red-600 border-white shadow-xl shadow-red-900/50 scale-[1.02]' : 'bg-zinc-900 border-white/5 hover:bg-zinc-800'}`}>
                         <div className="flex justify-between items-center mb-2">
                            <p className="text-2xl font-black italic uppercase">{w.name}</p>
                            {loadout.id === w.id && <ChevronRight size={24}/>}
                         </div>
                         <p className="text-xs font-bold opacity-80 uppercase tracking-widest">{w.desc}</p>
                         <div className="mt-4 flex gap-4">
                            <span className="text-xs bg-black/30 px-3 py-1 rounded">DMG: {w.damage}</span>
                            <span className="text-xs bg-black/30 px-3 py-1 rounded">HEAD: {w.headshot}x</span>
                         </div>
                       </div>
                    ))}
                 </div>

                 <div className="bg-zinc-900/40 rounded-3xl border border-white/5 p-10 flex flex-col justify-center items-center relative overflow-hidden min-h-[400px]">
                    <div className="absolute inset-0 bg-red-600/5 blur-[100px]"></div>
                    <div className="relative z-10 text-center">
                       <p className="text-xs font-black text-red-500 uppercase tracking-[0.4em] mb-4">Equipped</p>
                       <h3 className="text-6xl sm:text-8xl font-black italic uppercase leading-none drop-shadow-2xl">{loadout.name}</h3>
                    </div>
                 </div>

                 <button onClick={() => setScreen('menu')} className="lg:col-span-2 flex items-center gap-2 text-zinc-500 hover:text-white font-bold transition-colors uppercase mt-4"><ArrowLeft size={20}/> Save & Return</button>
              </div>
            )}
          </div>
        </div>
      )}

      {/* THREEJS RENDERER MOUNT */}
      <div ref={mountRef} className={`w-full h-full absolute inset-0 z-0 ${screen === 'game' ? 'block' : 'hidden'}`} />
    </div>
  );
}
import React, { useState, useEffect, useRef } from 'react';
import * as THREE from 'three';
import { 
  Crosshair, Target, Zap, ArrowLeft, Settings, Shield, Sword, 
  User, ChevronRight, LayoutGrid, Bomb, Skull, RotateCcw, MousePointer2, Trophy, Frown
} from 'lucide-react';

// --- WEAPON DATA (Wiki Accurate) ---
const WEAPONS = {
  PRIMARY: [
    { id: 'ar', name: 'Assault Rifle', damage: 18, headshot: 1.5, rps: 11, color: '#3b82f6', desc: 'Fast fire rate, reliable.' },
    { id: 'sniper', name: 'Sniper', damage: 85, headshot: 3.0, rps: 0.7, color: '#111111', desc: '1-shot headshot (255 DMG).' },
    { id: 'shotgun', name: 'Shotgun', damage: 10, headshot: 1.2, pellets: 8, color: '#450a0a', desc: 'Devastating up close.' }
  ],
  SECONDARY: [{ id: 'revolver', name: 'Revolver', damage: 45, headshot: 1.5, desc: 'High damage sidearm.' }],
  MELEE: [{ id: 'knife', name: 'Knife', damage: 55, desc: 'Close combat blade.' }],
  UTILITY: [{ id: 'grenade', name: 'Grenade', damage: 120, desc: 'Explosive throw.' }]
};

const GAME_MODES = [
  { id: '1v1', label: '1v1 DUEL', bots: 1, allies: 0 },
  { id: '2v2', label: '2v2 ARENA', bots: 2, allies: 1 },
  { id: '3v3', label: '3v3 SKIRMISH', bots: 3, allies: 2 },
  { id: '4v4', label: '4v4 BATTLE', bots: 4, allies: 3 },
  { id: '5v5', label: '5v5 WAR', bots: 5, allies: 4 }
];

export default function App() {
  const [screen, setScreen] = useState('menu'); // menu, mode_select, loadout, game, match_over
  const [round, setRound] = useState(1);
  const [scores, setScores] = useState({ player: 0, enemy: 0 });
  const [matchStatus, setMatchStatus] = useState(''); // '', 'ROUND WON', 'ROUND LOST', 'MATCH WON', 'MATCH LOST'
  const [mode, setMode] = useState(GAME_MODES[0]);
  const [loadout, setLoadout] = useState(WEAPONS.PRIMARY[0]);
  const [stats, setStats] = useState({ hp: 100, kills: 0 });
  const [isMobile, setIsMobile] = useState(false);
  const [isAiming, setIsAiming] = useState(false);
  const [isLocked, setIsLocked] = useState(false);

  const mountRef = useRef(null);
  const gameActive = useRef(false);
  const gameState = useRef({ isDead: false, roundEnded: false, spectating: null });
  const moveJoy = useRef({ x: 0, y: 0 });
  const lastTouch = useRef({ x: 0, y: 0 }); // For mobile looking

  // Detect mobile
  useEffect(() => {
    setIsMobile('ontouchstart' in window || navigator.maxTouchPoints > 0);
  }, []);

  // --- CORE GAME ENGINE ---
  useEffect(() => {
    if (screen !== 'game' || !mountRef.current) return;

    // Reset loop state for new round
    gameState.current = { isDead: false, roundEnded: false, spectating: null };
    setStats(s => ({ ...s, hp: 100 }));
    setMatchStatus('');

    // 1. SCENE SETUP
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x0a0a0c);
    scene.fog = new THREE.Fog(0x0a0a0c, 10, 150);

    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    mountRef.current.appendChild(renderer.domElement);

    // 2. LIGHTING
    scene.add(new THREE.AmbientLight(0xffffff, 0.4));
    const sun = new THREE.DirectionalLight(0xffffff, 0.8);
    sun.position.set(50, 100, 50);
    scene.add(sun);

    // 3. ARENA GENERATION & WALLS
    const walls = [];
    const floor = new THREE.Mesh(
      new THREE.PlaneGeometry(200, 200), 
      new THREE.MeshPhongMaterial({ color: 0x111111, shininess: 10 })
    );
    floor.rotation.x = -Math.PI / 2;
    scene.add(floor);

    const wallMat = new THREE.MeshPhongMaterial({ color: 0xffffff }); // Rivals White Walls
    const createWall = (w, h, d, x, z) => {
      const b = new THREE.Mesh(new THREE.BoxGeometry(w, h, d), wallMat);
      b.position.set(x, h/2, z);
      b.userData = { isWall: true };
      scene.add(b);
      walls.push(b);
      return b;
    };
    
    // Bounds
    createWall(200, 20, 2, 0, 100);
    createWall(200, 20, 2, 0, -100);
    createWall(2, 20, 200, 100, 0);
    createWall(2, 20, 200, -100, 0);

    // Random Cover/Blocks
    for(let i=0; i<30; i++) {
      createWall(4+Math.random()*8, 10, 4+Math.random()*8, Math.random()*160-80, Math.random()*160-80);
    }

    // Collision Helper (AABB)
    const canMoveTo = (pos, radius = 0.6) => {
      for(let w of walls) {
         const bx = w.position.x;
         const bz = w.position.z;
         const bw = w.geometry.parameters.width / 2;
         const bd = w.geometry.parameters.depth / 2;
         if (pos.x > bx - bw - radius && pos.x < bx + bw + radius && 
             pos.z > bz - bd - radius && pos.z < bz + bd + radius) {
             return false;
         }
      }
      return true;
    };

    // 4. ENTITIES (NPCs)
    let entities = [];
    const spawnEntity = (team, x, z) => {
      const g = new THREE.Group();
      
      const body = new THREE.Mesh(
        new THREE.BoxGeometry(1.2, 2, 0.8), 
        new THREE.MeshPhongMaterial({ color: team === 'red' ? 0xff3333 : 0x33ff33 })
      );
      body.position.y = 1;
      g.add(body);
      
      const head = new THREE.Mesh(
        new THREE.BoxGeometry(0.8, 0.8, 0.8), 
        new THREE.MeshPhongMaterial({ color: 0xffdbac })
      );
      head.position.y = 2.4;
      head.name = "HEAD";
      g.add(head);
      
      const gun = new THREE.Mesh(new THREE.BoxGeometry(0.2, 0.3, 1), new THREE.MeshPhongMaterial({ color: 0x222222 }));
      gun.position.set(0.7, 1.2, 0.5);
      g.add(gun);

      g.position.set(x, 0, z);
      g.userData = { isEntity: true };
      scene.add(g);
      
      entities.push({ 
          mesh: g, team, hp: 100, lastShot: Date.now() + Math.random()*2000, 
          isDead: false, targetPos: new THREE.Vector3(x, 0, z) 
      });
    };

    for(let i=0; i<mode.bots; i++) spawnEntity('red', Math.random()*60-30, -50 - Math.random()*20);
    for(let i=0; i<mode.allies; i++) spawnEntity('green', Math.random()*40-20, 40 + Math.random()*10);

    // 5. PLAYER VARIABLES & CONTROLS
    camera.position.set(0, 1.8, 80);
    let pitch = 0, yaw = 0;
    const keys = {};

    const onKeyDown = (e) => keys[e.code] = true;
    const onKeyUp = (e) => keys[e.code] = false;

    const onMouseMove = (e) => {
      if (document.pointerLockElement === renderer.domElement && !gameState.current.isDead) {
        yaw -= e.movementX * 0.002;
        pitch -= e.movementY * 0.002;
        pitch = Math.max(-1.5, Math.min(1.5, pitch));
        if (!isLocked) setIsLocked(true);
      } else {
        setIsLocked(false);
      }
    };

    window.handleMobileLookStart = (e) => { lastTouch.current = { x: e.touches[0].clientX, y: e.touches[0].clientY }; };
    window.handleMobileLookMove = (e) => {
      if(gameState.current.isDead) return;
      const touch = e.touches[0];
      yaw -= (touch.clientX - lastTouch.current.x) * 0.004;
      pitch -= (touch.clientY - lastTouch.current.y) * 0.004;
      pitch = Math.max(-1.5, Math.min(1.5, pitch));
      lastTouch.current = { x: touch.clientX, y: touch.clientY };
    };

    // Smooth Tracers System
    const tracers = [];
    const addTracer = (start, end, isEnemy) => {
         const geo = new THREE.BufferGeometry().setFromPoints([start, start]);
         const mat = new THREE.LineBasicMaterial({ color: isEnemy ? 0xff4444 : 0xffffaa, transparent: true, opacity: 0.8 });
         const line = new THREE.Line(geo, mat);
         scene.add(line);
         tracers.push({ line, start, end, progress: 0 });
    };

    // Firing Logic (Raycast checks Walls + NPCs)
    const fire = () => {
      if (gameState.current.isDead || gameState.current.roundEnded) return;

      const ray = new THREE.Raycaster();
      ray.setFromCamera(new THREE.Vector2(0, 0), camera); // Perfect center aim
      
      const targets = [...walls, ...entities.map(e => e.mesh)];
      const hits = ray.intersectObjects(targets, true);

      pitch += 0.04; // Visual Recoil

      // Tracer Origin
      const startPos = camera.position.clone().add(new THREE.Vector3(0.3, -0.3, -1).applyQuaternion(camera.quaternion));
      
      if (hits.length > 0) {
        const hit = hits[0];
        addTracer(startPos, hit.point, false);

        if (!hit.object.userData.isWall && hit.object.parent) {
            // Hit Entity
            const parentMesh = hit.object.parent;
            const targetEnt = entities.find(e => e.mesh === parentMesh);
            
            if (targetEnt && !targetEnt.isDead) {
                const isHead = hit.object.name === "HEAD";
                let damage = loadout.damage;
                if (isHead) damage *= loadout.headshot;

                const spark = new THREE.Mesh(new THREE.SphereGeometry(0.2), new THREE.MeshBasicMaterial({color: isHead ? 0xff0000 : 0xffff00}));
                spark.position.copy(hit.point);
                scene.add(spark);
                setTimeout(() => scene.remove(spark), 100);

                targetEnt.hp -= damage;
                
                // Death check
                if (targetEnt.hp <= 0) {
                    targetEnt.isDead = true;
                    scene.remove(targetEnt.mesh);
                    if (targetEnt.team === 'red') setStats(s => ({ ...s, kills: s.kills + 1 }));
                }
            }
        }
      } else {
         addTracer(startPos, ray.ray.at(100, new THREE.Vector3()), false);
      }
    };

    window.addEventListener('keydown', onKeyDown);
    window.addEventListener('keyup', onKeyUp);
    window.addEventListener('mousemove', onMouseMove);
    window.addEventListener('mousedown', (e) => {
        if(e.button === 0 && document.pointerLockElement) fire();
        if(e.button === 2) setIsAiming(true);
    });
    window.addEventListener('mouseup', (e) => { if(e.button === 2) setIsAiming(false); });
    window.mobileFire = fire;
    window.requestLock = () => renderer.domElement.requestPointerLock();

    // Round End Handler
    const handleRoundEnd = (winnerTeam) => {
        gameState.current.roundEnded = true;
        setMatchStatus(winnerTeam === 'player' ? 'ROUND WON' : 'ROUND LOST');
        
        setTimeout(() => {
            setScores(prev => {
                const updated = { ...prev, [winnerTeam]: prev[winnerTeam] + 1 };
                if (updated.player >= 4 || updated.enemy >= 4) {
                    setMatchStatus(updated.player >= 4 ? 'MATCH WON' : 'MATCH LOST');
                    setTimeout(() => setScreen('match_over'), 2000);
                } else {
                    setRound(r => r + 1); // Triggers remount for next round
                }
                return updated;
            });
        }, 2500);
    };

    // 6. ANIMATION LOOP
    const animate = () => {
      if (!gameActive.current) return;
      requestAnimationFrame(animate);

      // Alive teams check
      const aliveRed = entities.filter(e => e.team === 'red' && !e.isDead);
      const aliveGreen = entities.filter(e => e.team === 'green' && !e.isDead);

      if (!gameState.current.roundEnded) {
         if (aliveRed.length === 0) handleRoundEnd('player');
         else if (gameState.current.isDead && aliveGreen.length === 0) handleRoundEnd('enemy');
      }

      // PLAYER CAMERA / MOVEMENT
      if (!gameState.current.isDead) {
          camera.rotation.set(pitch, yaw, 0, 'YXZ');
          
          const xInput = (keys['KeyD'] ? 1 : 0) - (keys['KeyA'] ? 1 : 0) + moveJoy.current.x;
          const zInput = (keys['KeyS'] ? 1 : 0) - (keys['KeyW'] ? 1 : 0) + moveJoy.current.y;
          const speed = keys['ShiftLeft'] ? 0.35 : 0.15;
          
          if (xInput !== 0 || zInput !== 0) {
            const inputVec = new THREE.Vector3(xInput, 0, zInput).normalize();
            inputVec.applyAxisAngle(new THREE.Vector3(0, 1, 0), yaw);

            const nextX = camera.position.clone(); nextX.x += inputVec.x * speed;
            const nextZ = camera.position.clone(); nextZ.z += inputVec.z * speed;
            
            if (Math.abs(inputVec.x) > 0 && canMoveTo(nextX)) camera.position.x = nextX.x;
            if (Math.abs(inputVec.z) > 0 && canMoveTo(nextZ)) camera.position.z = nextZ.z;
          }
          camera.position.y = 1.8;

          // Smooth Aiming FOV
          camera.fov = THREE.MathUtils.lerp(camera.fov, isAiming ? 40 : 75, 0.15);
          camera.updateProjectionMatrix();

      } else {
          // SPECTATOR MODE
          if (!gameState.current.spectating || gameState.current.spectating.isDead) {
             const available = entities.filter(e => !e.isDead);
             gameState.current.spectating = available.length > 0 ? available[0] : null;
          }
          
          if (gameState.current.spectating) {
              const targetPos = gameState.current.spectating.mesh.position;
              const time = Date.now() * 0.001;
              const offset = new THREE.Vector3(Math.cos(time)*6, 4, Math.sin(time)*6);
              camera.position.lerp(targetPos.clone().add(offset), 0.05);
              camera.lookAt(targetPos.clone().add(new THREE.Vector3(0, 1.5, 0)));
          } else {
              // Everyone dead, fall to ground
              camera.position.y = THREE.MathUtils.lerp(camera.position.y, 0.2, 0.05);
              camera.rotation.z = THREE.MathUtils.lerp(camera.rotation.z, Math.PI/4, 0.02);
          }
      }

      // TRACERS ANIMATION
      for (let i = tracers.length - 1; i >= 0; i--) {
          const t = tracers[i];
          t.progress += 0.2;
          const curStart = t.start.clone().lerp(t.end, Math.min(1, t.progress - 0.2));
          const curEnd = t.start.clone().lerp(t.end, Math.min(1, t.progress));
          t.line.geometry.setFromPoints([curStart, curEnd]);
          if (t.progress >= 1.2) {
              scene.remove(t.line);
              tracers.splice(i, 1);
          }
      }

      // NPC AI & MOVEMENT
      entities.forEach(ent => {
        if (ent.isDead) return;

        // Determine target
        let targetPos = null;
        if (ent.team === 'red') targetPos = gameState.current.isDead ? (aliveGreen[0]?.mesh.position || null) : camera.position;
        else targetPos = aliveRed[0]?.mesh.position || null; // Green attacks red

        if (targetPos) {
            const dist = ent.mesh.position.distanceTo(targetPos);
            ent.mesh.lookAt(targetPos.x, 1, targetPos.z);
            
            // Move logic
            const dir = targetPos.clone().sub(ent.mesh.position).normalize();
            ent.targetPos.copy(ent.mesh.position);

            if (dist > 15) ent.targetPos.add(dir.multiplyScalar(0.08));
            else if (dist < 8) ent.targetPos.add(dir.multiplyScalar(-0.05));

            // Apply AABB Collision to NPC
            const nX = new THREE.Vector3(ent.targetPos.x, 0, ent.mesh.position.z);
            const nZ = new THREE.Vector3(ent.mesh.position.x, 0, ent.targetPos.z);
            if (!canMoveTo(nX, 0.6)) ent.targetPos.x = ent.mesh.position.x;
            if (!canMoveTo(nZ, 0.6)) ent.targetPos.z = ent.mesh.position.z;

            // Smooth Interpolated Movement
            ent.mesh.position.lerp(ent.targetPos, 0.2);

            // Shoot Logic
            const now = Date.now();
            if (dist < 60 && now - ent.lastShot > 1800) {
                ent.lastShot = now;

                const gunOffset = new THREE.Vector3(0, 1.5, 0);
                const start = ent.mesh.position.clone().add(gunOffset);
                const end = targetPos.clone().add(gunOffset);
                addTracer(start, end, ent.team === 'red');

                // Visual Muzzle Flash
                const flash = new THREE.Mesh(new THREE.SphereGeometry(0.3), new THREE.MeshBasicMaterial({color: 0xffaa00}));
                flash.position.copy(start);
                scene.add(flash);
                setTimeout(() => scene.remove(flash), 100);

                // Accuracy checks via raycast to ensure wall cover works
                const nRay = new THREE.Raycaster(start, end.clone().sub(start).normalize());
                const nHits = nRay.intersectObjects(walls);
                
                if (nHits.length === 0 || nHits[0].distance > dist) {
                     // Wall didn't block it
                     if (ent.team === 'red' && targetPos === camera.position && Math.random() > 0.6) {
                         setStats(s => {
                             const nHp = s.hp - 15;
                             if (nHp <= 0 && !gameState.current.isDead) {
                                 gameState.current.isDead = true;
                                 document.exitPointerLock?.();
                             }
                             return { ...s, hp: nHp };
                         });
                     } else if (targetPos !== camera.position && Math.random() > 0.5) {
                         // Bot hits Bot
                         const targetEnt = entities.find(e => e.mesh.position === targetPos);
                         if (targetEnt) {
                             targetEnt.hp -= 20;
                             if (targetEnt.hp <= 0) {
                                targetEnt.isDead = true;
                                scene.remove(targetEnt.mesh);
                             }
                         }
                     }
                }
            }
        }
      });

      renderer.render(scene, camera);
    };

    gameActive.current = true;
    animate();

    return () => {
      gameActive.current = false;
      window.removeEventListener('keydown', onKeyDown);
      window.removeEventListener('keyup', onKeyUp);
      window.removeEventListener('mousemove', onMouseMove);
      if (mountRef.current) mountRef.current.innerHTML = '';
    };
  }, [screen, round, mode, loadout]); // Scene fully rebuilds on round change

  const initMatch = (selectedMode) => {
    setMode(selectedMode);
    setScores({ player: 0, enemy: 0 });
    setRound(1);
    setStats({ hp: 100, kills: 0 });
    setScreen('game');
  };

  // --- UI RENDER ---
  return (
    <div className="w-full h-screen bg-black text-white font-sans overflow-hidden select-none touch-none">
      
      {/* --- MATCH OVER SCREEN --- */}
      {screen === 'match_over' && (
        <div className="absolute inset-0 z-[100] bg-black/95 backdrop-blur-2xl flex flex-col items-center justify-center p-10 animate-in fade-in duration-500">
          {scores.player >= 4 ? <Trophy size={140} className="text-yellow-500 mb-6 drop-shadow-[0_0_30px_rgba(234,179,8,0.5)]" /> : <Frown size={140} className="text-red-600 mb-6 drop-shadow-[0_0_30px_rgba(220,38,38,0.5)]" />}
          <h1 className={`text-7xl sm:text-9xl font-black italic tracking-tighter mb-2 drop-shadow-2xl ${scores.player >= 4 ? 'text-white' : 'text-red-600'}`}>
             {scores.player >= 4 ? 'VICTORY' : 'DEFEAT'}
          </h1>
          <p className="text-zinc-400 font-bold uppercase tracking-[0.3em] mb-10 text-center text-xl">
             FINAL SCORE: {scores.player} - {scores.enemy}
          </p>
          
          <button onClick={() => setScreen('menu')} className="bg-white text-black px-12 py-5 font-black italic text-2xl flex items-center justify-center gap-3 hover:scale-105 transition-transform skew-x-[-10deg]">
             <RotateCcw size={28}/> RETURN TO MENU
          </button>
        </div>
      )}

      {/* --- GAME HUD --- */}
      {screen === 'game' && (
        <div className="absolute inset-0 z-50 pointer-events-none">
          
          {/* Round End Overlay */}
          {matchStatus && (
             <div className="absolute inset-0 bg-black/60 flex items-center justify-center z-50 animate-in zoom-in-90 duration-300">
                <h2 className="text-6xl sm:text-8xl font-black italic text-white tracking-widest drop-shadow-[0_0_20px_rgba(255,255,255,0.8)] uppercase">{matchStatus}</h2>
             </div>
          )}

          {/* Match Score UI (Top Left) */}
          <div className="absolute top-6 left-6 flex gap-4">
             <div className="bg-blue-600/80 px-6 py-3 rounded-lg border-2 border-white/20 skew-x-[-10deg] flex flex-col items-center min-w-[100px] shadow-xl">
                 <span className="text-[10px] font-bold uppercase tracking-widest text-white/80">Ally Team</span>
                 <span className="text-4xl font-black">{scores.player}</span>
             </div>
             <div className="bg-red-600/80 px-6 py-3 rounded-lg border-2 border-white/20 skew-x-[-10deg] flex flex-col items-center min-w-[100px] shadow-xl">
                 <span className="text-[10px] font-bold uppercase tracking-widest text-white/80">Enemy Team</span>
                 <span className="text-4xl font-black">{scores.enemy}</span>
             </div>
          </div>

          {/* Match Point Warning */}
          {(scores.player === 3 || scores.enemy === 3) && !matchStatus && (
              <div className="absolute top-24 left-6 text-yellow-500 font-black italic tracking-widest animate-pulse border-l-4 border-yellow-500 pl-3">
                  MATCH POINT
              </div>
          )}

          {/* Lock Mouse Prompt (PC) */}
          {!isMobile && !isLocked && !matchStatus && !gameState.current.isDead && (
             <div className="absolute inset-0 bg-black/50 backdrop-blur-sm flex items-center justify-center pointer-events-auto">
                <button onClick={() => window.requestLock()} className="bg-red-600 px-10 py-6 rounded-2xl font-black italic text-3xl flex items-center gap-4 hover:scale-105 transition-all shadow-2xl shadow-red-600/50">
                   <MousePointer2 size={40}/> CLICK TO LOCK VIEW
                </button>
             </div>
          )}

          {/* Fixed CSS Mini Crosshair (Replaces old dynamic box) */}
          {!gameState.current.isDead && (
             <div className="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 flex items-center justify-center mix-blend-difference">
                 <div className={`absolute w-5 h-0.5 bg-white transition-transform duration-200 ${isAiming ? 'scale-50' : ''}`}></div>
                 <div className={`absolute w-0.5 h-5 bg-white transition-transform duration-200 ${isAiming ? 'scale-50' : ''}`}></div>
             </div>
          )}

          {/* Spectator Indicator */}
          {gameState.current.isDead && !matchStatus && (
             <div className="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 mt-32 bg-black/80 text-white px-6 py-2 rounded-full border border-white/20 font-bold tracking-widest uppercase animate-pulse">
                Spectating (Dead)
             </div>
          )}

          {/* Health Bar */}
          {!gameState.current.isDead && (
            <div className="absolute bottom-10 left-10 p-6 bg-black/80 border-l-4 border-red-600 skew-x-[-10deg]">
              <div className="text-[10px] font-black text-red-500 uppercase tracking-widest">Health</div>
              <div className="w-48 sm:w-64 h-3 bg-zinc-800 mt-1"><div className="h-full bg-red-600 transition-all duration-300" style={{width: `${Math.max(0, stats.hp)}%`}}></div></div>
              <div className="mt-2 text-xs font-bold text-white flex items-center gap-2">
                 ROUND {round} (FIRST TO 4)
              </div>
            </div>
          )}

          {/* Score & Mode HUD */}
          <div className="absolute top-10 left-1/2 -translate-x-1/2 flex items-center gap-10 bg-zinc-900/90 px-10 py-3 rounded-full border border-white/10 shadow-xl">
            <div className="text-center"><div className="text-[10px] font-bold text-zinc-500 tracking-widest">MY KILLS</div><div className="text-3xl font-black text-red-600 leading-none">{stats.kills}</div></div>
            <div className="w-px h-8 bg-white/10"></div>
            <div className="text-center"><div className="text-[10px] font-bold text-zinc-500 tracking-widest">MODE</div><div className="text-xl font-black text-white pt-1">{mode.label}</div></div>
          </div>

          {/* Weapon Name */}
          {!gameState.current.isDead && (
            <div className="absolute bottom-10 right-10 text-right uppercase italic font-black">
               <div className="text-5xl leading-none">{loadout.name}</div>
               <div className="text-red-500 text-lg">∞ AMMO</div>
            </div>
          )}

          {/* Mobile Overlay */}
          {isMobile && !gameState.current.isDead && (
            <>
              {/* Look Area (Right Half) */}
              <div className="absolute top-0 right-0 w-1/2 h-full z-40 pointer-events-auto touch-none"
                   onTouchStart={(e) => window.handleMobileLookStart(e)}
                   onTouchMove={(e) => window.handleMobileLookMove(e)}
              />

              {/* Move Joystick */}
              <div className="absolute bottom-12 left-12 w-32 h-32 bg-white/5 rounded-full border border-white/10 z-50 pointer-events-auto touch-none"
                onTouchMove={(e) => {
                  const t = e.touches[0]; const r = e.target.getBoundingClientRect();
                  moveJoy.current = { x: (t.clientX - (r.left + 64))/64, y: (t.clientY - (r.top + 64))/64 };
                }}
                onTouchEnd={() => moveJoy.current = {x:0, y:0}}
              />

              {/* Action Buttons */}
              <div className="absolute right-12 bottom-32 flex flex-col gap-4 z-50 pointer-events-auto">
                <button onTouchStart={() => window.mobileFire()} className="w-20 h-20 bg-red-600 rounded-full flex items-center justify-center border-4 border-white/20 active:scale-90 shadow-2xl shadow-red-900"><Zap fill="white" size={32}/></button>
                <button onTouchStart={() => setIsAiming(true)} onTouchEnd={() => setIsAiming(false)} className="w-16 h-16 bg-zinc-800 rounded-full flex items-center justify-center border-2 border-white/10 active:scale-90"><Target size={24}/></button>
              </div>
            </>
          )}

          {/* Quit to Menu */}
          <button onClick={() => { setScreen('menu'); document.exitPointerLock?.(); }} className="absolute top-6 right-6 pointer-events-auto bg-zinc-900 border border-white/10 px-4 py-2 text-xs font-bold uppercase rounded hover:bg-red-600 transition-colors">QUIT</button>
        </div>
      )}

      {/* --- MENU / LOBBY --- */}
      {screen !== 'game' && screen !== 'match_over' && (
        <div className="w-full h-full bg-[radial-gradient(circle_at_center,_#2a0a0a_0%,_#000000_100%)] flex flex-col">
          
          {/* Header */}
          <div className="p-6 flex justify-between items-center bg-black/50 backdrop-blur-md border-b border-white/5">
            <div className="flex items-center gap-4">
               <div className="w-12 h-12 bg-red-600 flex items-center justify-center font-black text-2xl italic rounded shadow-lg shadow-red-600/50">R</div>
               <div>
                  <h1 className="text-2xl font-black italic tracking-tighter leading-none">RIVALS</h1>
                  <p className="text-[10px] text-red-500 font-bold uppercase tracking-widest">Pro Arena</p>
               </div>
            </div>
            <div className="bg-zinc-900 px-6 py-2 rounded-full border border-white/10 text-xs font-bold text-yellow-500">12,500 CR</div>
          </div>

          <div className="flex-1 flex flex-col items-center justify-center p-6 overflow-y-auto">
            
            {/* MAIN MENU */}
            {screen === 'menu' && (
              <div className="flex flex-col items-center gap-12 w-full max-w-sm">
                 <div className="text-center w-full">
                   <h2 className="text-7xl sm:text-9xl font-black italic tracking-tighter text-white drop-shadow-2xl select-none">RIVALS</h2>
                   <div className="h-2 w-full bg-red-600 mt-[-10px] sm:mt-[-15px] skew-x-[-15deg]"></div>
                 </div>
                 
                 <div className="flex flex-col gap-4 w-full">
                   <button onClick={() => setScreen('mode_select')} className="w-full bg-red-600 py-6 text-3xl font-black italic skew-x-[-10deg] border-b-8 border-red-900 hover:bg-white hover:text-red-600 transition-all active:translate-y-2 active:border-b-0 shadow-2xl">PLAY</button>
                   <button onClick={() => setScreen('loadout')} className="w-full bg-zinc-900 py-4 font-bold text-xl skew-x-[-10deg] border-b-4 border-black hover:bg-zinc-800 transition-colors">ARMORY</button>
                 </div>
              </div>
            )}

            {/* MODE SELECTOR */}
            {screen === 'mode_select' && (
              <div className="w-full max-w-6xl">
                <h2 className="text-4xl font-black italic mb-8 text-center sm:text-left">SELECT MODE (FIRST TO 4)</h2>
                <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-5 gap-6">
                  {GAME_MODES.map(m => (
                    <div key={m.id} onClick={() => initMatch(m)} 
                      className="group bg-zinc-900/50 border-2 border-white/5 hover:border-red-600 p-8 rounded-2xl cursor-pointer transition-all hover:-translate-y-2 relative overflow-hidden">
                      <div className="absolute inset-0 bg-red-600/0 group-hover:bg-red-600/10 transition-colors"></div>
                      <LayoutGrid size={48} className="text-red-600 mb-6 group-hover:scale-110 transition-transform" />
                      <h3 className="text-2xl font-black italic">{m.label}</h3>
                      <p className="text-zinc-500 text-xs font-bold mt-2">VS {m.bots} BOTS</p>
                    </div>
                  ))}
                </div>
                <button onClick={() => setScreen('menu')} className="mt-12 flex items-center gap-2 text-zinc-500 hover:text-white font-bold transition-colors uppercase"><ArrowLeft size={20}/> Back to Menu</button>
              </div>
            )}

            {/* ARMORY / LOADOUT */}
            {screen === 'loadout' && (
              <div className="w-full max-w-5xl grid grid-cols-1 lg:grid-cols-2 gap-10">
                 <div className="space-y-4">
                    <h2 className="text-4xl font-black italic mb-6">PRIMARY WEAPON</h2>
                    {WEAPONS.PRIMARY.map(w => (
                       <div key={w.id} onClick={() => setLoadout(w)}
                         className={`p-6 rounded-xl border-2 flex flex-col justify-center cursor-pointer transition-all ${loadout.id === w.id ? 'bg-red-600 border-white shadow-xl shadow-red-900/50 scale-[1.02]' : 'bg-zinc-900 border-white/5 hover:bg-zinc-800'}`}>
                         <div className="flex justify-between items-center mb-2">
                            <p className="text-2xl font-black italic uppercase">{w.name}</p>
                            {loadout.id === w.id && <ChevronRight size={24}/>}
                         </div>
                         <p className="text-xs font-bold opacity-80 uppercase tracking-widest">{w.desc}</p>
                         <div className="mt-4 flex gap-4">
                            <span className="text-xs bg-black/30 px-3 py-1 rounded">DMG: {w.damage}</span>
                            <span className="text-xs bg-black/30 px-3 py-1 rounded">HEAD: {w.headshot}x</span>
                         </div>
                       </div>
                    ))}
                 </div>

                 <div className="bg-zinc-900/40 rounded-3xl border border-white/5 p-10 flex flex-col justify-center items-center relative overflow-hidden min-h-[400px]">
                    <div className="absolute inset-0 bg-red-600/5 blur-[100px]"></div>
                    <div className="relative z-10 text-center">
                       <p className="text-xs font-black text-red-500 uppercase tracking-[0.4em] mb-4">Equipped</p>
                       <h3 className="text-6xl sm:text-8xl font-black italic uppercase leading-none drop-shadow-2xl">{loadout.name}</h3>
                    </div>
                 </div>

                 <button onClick={() => setScreen('menu')} className="lg:col-span-2 flex items-center gap-2 text-zinc-500 hover:text-white font-bold transition-colors uppercase mt-4"><ArrowLeft size={20}/> Save & Return</button>
              </div>
            )}
          </div>
        </div>
      )}

      {/* THREEJS RENDERER MOUNT */}
      <div ref={mountRef} className={`w-full h-full absolute inset-0 z-0 ${screen === 'game' ? 'block' : 'hidden'}`} />
    </div>
  );
}
