<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HKDSE Biology: Antagonistic Muscles Simulator</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; background-color: #f8fafc; }
        .muscle-path {
            transition: d 0.1s ease-out, fill 0.1s ease-out;
        }
        .slider-control {
            appearance: none;
            width: 100%;
            height: 8px;
            background: #e2e8f0;
            border-radius: 4px;
            outline: none;
        }
        .slider-control::-webkit-slider-thumb {
            appearance: none;
            width: 20px;
            height: 20px;
            background: #3b82f6;
            border-radius: 50%;
            cursor: pointer;
            transition: background 0.15s;
        }
        .slider-control::-webkit-slider-thumb:hover { background: #2563eb; }
        .tab-btn { transition: all 0.2s; }
        .tab-btn.active {
            background-color: #2563eb;
            color: white;
            border-color: #2563eb;
        }
    </style>
</head>
<body class="min-h-screen text-slate-800 p-4 md:p-8">

    <div class="max-w-6xl mx-auto space-y-6">
        
        <!-- Header & Educational Panel -->
        <div class="bg-white rounded-2xl shadow-sm border border-slate-200 overflow-hidden">
            <div class="bg-blue-600 p-6 text-white">
                <h1 class="text-3xl font-bold">HKDSE Biology: Antagonistic Muscles</h1>
                <p class="text-blue-100 mt-2 text-lg">Interactive Simulator for Flexors, Extensors, and Joint Movement</p>
            </div>
            
            <div class="p-6 md:p-8 grid grid-cols-1 md:grid-cols-2 gap-6 bg-slate-50">
                <div class="bg-white p-5 rounded-xl border border-slate-200 shadow-sm border-l-4 border-l-rose-500">
                    <h2 class="text-xl font-bold text-rose-600 mb-2">Flexor Muscle</h2>
                    <p class="text-slate-600 leading-relaxed">
                        A muscle that <strong>contracts to bend a joint</strong>, decreasing the angle between the bones. <br>
                        <em>Example:</em> Biceps (bends the arm at the elbow), Hamstrings (bends the leg at the knee).
                    </p>
                </div>
                <div class="bg-white p-5 rounded-xl border border-slate-200 shadow-sm border-l-4 border-l-emerald-500">
                    <h2 class="text-xl font-bold text-emerald-600 mb-2">Extensor Muscle</h2>
                    <p class="text-slate-600 leading-relaxed">
                        A muscle that <strong>contracts to straighten a joint</strong>, increasing the angle between the bones. <br>
                        <em>Example:</em> Triceps (straightens the arm), Quadriceps (straightens the leg at the knee).
                    </p>
                </div>
                <div class="col-span-1 md:col-span-2 bg-blue-50 p-5 rounded-xl border border-blue-100">
                    <h3 class="font-bold text-blue-800 mb-1">Antagonistic Pairs (HKDSE Principle)</h3>
                    <p class="text-sm text-blue-900">Muscles can only pull (contract) and cannot push. Therefore, to move a bone in two opposite directions at a joint, at least two muscles are required. As one muscle contracts (getting shorter, thicker, and generating force), its partner relaxes (stretching and thinning).</p>
                </div>
            </div>
        </div>

        <!-- Simulator Area -->
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
            
            <!-- Left: SVG Canvas -->
            <div class="lg:col-span-2 bg-white rounded-2xl shadow-sm border border-slate-200 overflow-hidden flex flex-col h-[700px]">
                <div class="flex border-b border-slate-200 bg-slate-50 p-4 gap-4 justify-center">
                    <button id="tab-arm" class="tab-btn active px-6 py-2 rounded-full border border-slate-300 font-semibold focus:outline-none" onclick="switchTab('arm')">Arm (Elbow Joint)</button>
                    <button id="tab-leg" class="tab-btn px-6 py-2 rounded-full border border-slate-300 font-semibold text-slate-600 bg-white focus:outline-none" onclick="switchTab('leg')">Leg (Hip, Knee, Ankle)</button>
                </div>
                
                <div class="flex-grow relative bg-slate-800">
                    <!-- SVG Canvas -->
                    <svg id="canvas" class="w-full h-full" viewBox="0 0 600 700" preserveAspectRatio="xMidYMid meet">
                        <!-- Background Grid -->
                        <pattern id="grid" width="40" height="40" patternUnits="userSpaceOnUse">
                            <path d="M 40 0 L 0 0 0 40" fill="none" stroke="#334155" stroke-width="0.5"/>
                        </pattern>
                        <rect width="100%" height="100%" fill="url(#grid)" />
                        
                        <!-- Dynamic Elements Injected Here -->
                        <g id="draw-group"></g>
                    </svg>
                    
                    <!-- Overlays -->
                    <div class="absolute bottom-4 left-4 text-slate-400 text-sm flex items-center gap-2">
                        <div class="w-3 h-3 bg-red-600 rounded-full"></div> Contracted (Thick)
                        <div class="w-3 h-3 bg-red-200 rounded-full ml-3"></div> Relaxed (Thin)
                    </div>
                </div>
            </div>

            <!-- Right: Controls -->
            <div class="bg-white rounded-2xl shadow-sm border border-slate-200 p-6 flex flex-col">
                <h3 class="text-xl font-bold text-slate-800 mb-6 border-b pb-4">Joint Controls</h3>
                
                <!-- Arm Controls -->
                <div id="controls-arm" class="space-y-8">
                    <div>
                        <div class="flex justify-between items-end mb-2">
                            <label class="font-semibold text-slate-700">Elbow Angle</label>
                            <span id="val-elbow" class="text-blue-600 font-bold bg-blue-50 px-2 py-1 rounded">0°</span>
                        </div>
                        <input type="range" id="slider-elbow" class="slider-control" min="0" max="130" value="0" oninput="updateState('arm', 'elbow', this.value)">
                        <div class="flex justify-between text-xs text-slate-500 mt-2">
                            <span>Straight (0°)</span>
                            <span>Flexed (130°)</span>
                        </div>
                    </div>
                    
                    <div class="space-y-4">
                        <h4 class="font-bold text-slate-800 bg-slate-100 p-2 rounded">Pair 1: Arm Muscles</h4>
                        <div class="flex items-center justify-between p-3 rounded-lg border border-slate-100 bg-slate-50">
                            <span class="font-semibold text-slate-700">Biceps (Flexor)</span>
                            <span id="stat-biceps" class="text-sm font-bold px-3 py-1 rounded-full">Relaxed</span>
                        </div>
                        <div class="flex items-center justify-between p-3 rounded-lg border border-slate-100 bg-slate-50">
                            <span class="font-semibold text-slate-700">Triceps (Extensor)</span>
                            <span id="stat-triceps" class="text-sm font-bold px-3 py-1 rounded-full">Contracted</span>
                        </div>
                    </div>
                    
                    <div class="pt-4 grid grid-cols-2 gap-3">
                        <button onclick="animateSlider('slider-elbow', 0)" class="py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 font-semibold rounded-lg transition">Extend Fully</button>
                        <button onclick="animateSlider('slider-elbow', 130)" class="py-2 bg-blue-100 hover:bg-blue-200 text-blue-700 font-semibold rounded-lg transition">Flex Fully</button>
                    </div>
                </div>

                <!-- Leg Controls -->
                <div id="controls-leg" class="space-y-8 hidden overflow-y-auto pr-2">
                    <!-- Hip Slider -->
                    <div>
                        <div class="flex justify-between items-end mb-2">
                            <label class="font-semibold text-slate-700">Hip Angle</label>
                            <span id="val-hip" class="text-blue-600 font-bold bg-blue-50 px-2 py-1 rounded">0°</span>
                        </div>
                        <input type="range" id="slider-hip" class="slider-control" min="-20" max="90" value="0" oninput="updateState('leg', 'hip', this.value)">
                    </div>

                    <!-- Knee Slider -->
                    <div>
                        <div class="flex justify-between items-end mb-2">
                            <label class="font-semibold text-slate-700">Knee Angle</label>
                            <span id="val-knee" class="text-blue-600 font-bold bg-blue-50 px-2 py-1 rounded">0°</span>
                        </div>
                        <input type="range" id="slider-knee" class="slider-control" min="0" max="130" value="0" oninput="updateState('leg', 'knee', this.value)">
                    </div>

                    <!-- Ankle Slider -->
                    <div>
                        <div class="flex justify-between items-end mb-2">
                            <label class="font-semibold text-slate-700">Ankle Flexion</label>
                            <span id="val-ankle" class="text-blue-600 font-bold bg-blue-50 px-2 py-1 rounded">0°</span>
                        </div>
                        <input type="range" id="slider-ankle" class="slider-control" min="-30" max="40" value="0" oninput="updateState('leg', 'ankle', this.value)">
                    </div>

                    <div class="space-y-3">
                        <h4 class="font-bold text-slate-800 bg-slate-100 p-2 rounded">Muscle Pairs (Leg)</h4>
                        
                        <!-- Pair 4 (Order based on prompt mapping) -->
                        <div class="text-sm border-l-2 border-indigo-400 pl-3">
                            <p class="font-bold text-slate-700 mb-1">Pair 4: Hip Joint</p>
                            <div class="flex justify-between"><span class="text-slate-600">Iliopsoas (Flexor)</span> <span id="stat-iliopsoas" class="font-semibold text-xs">Relaxed</span></div>
                            <div class="flex justify-between"><span class="text-slate-600">Gluteus Max (Extensor)</span> <span id="stat-gluteus" class="font-semibold text-xs">Relaxed</span></div>
                        </div>

                        <!-- Pair 2 -->
                        <div class="text-sm border-l-2 border-emerald-400 pl-3 mt-2">
                            <p class="font-bold text-slate-700 mb-1">Pair 2: Knee Joint</p>
                            <div class="flex justify-between"><span class="text-slate-600">Hamstrings (Flexor)</span> <span id="stat-hamstrings" class="font-semibold text-xs">Relaxed</span></div>
                            <div class="flex justify-between"><span class="text-slate-600">Quadriceps (Extensor)</span> <span id="stat-quadriceps" class="font-semibold text-xs">Relaxed</span></div>
                        </div>

                        <!-- Pair 3 -->
                        <div class="text-sm border-l-2 border-amber-400 pl-3 mt-2">
                            <p class="font-bold text-slate-700 mb-1">Pair 3: Ankle Joint</p>
                            <div class="flex justify-between"><span class="text-slate-600">Tibialis Anterior (Flexor)</span> <span id="stat-tibialis" class="font-semibold text-xs">Relaxed</span></div>
                            <div class="flex justify-between"><span class="text-slate-600">Gastrocnemius (Extensor)</span> <span id="stat-gastrocnemius" class="font-semibold text-xs">Relaxed</span></div>
                        </div>
                    </div>
                </div>

            </div>
        </div>
    </div>

    <script>
        // --- Core State ---
        const state = {
            activeTab: 'arm',
            arm: { elbow: 0 },
            leg: { hip: 0, knee: 0, ankle: 0 }
        };

        // --- Math & Geometry Helpers ---
        const deg2rad = Math.PI / 180;

        // Colors
        const COLOR_RELAXED = [254, 202, 202]; // Tailwind red-200
        const COLOR_CONTRACTED = [220, 38, 38]; // Tailwind red-600

        function interpolateColor(c1, c2, factor) {
            const r = Math.round(c1[0] + factor * (c2[0] - c1[0]));
            const g = Math.round(c1[1] + factor * (c2[1] - c1[1]));
            const b = Math.round(c1[2] + factor * (c2[2] - c1[2]));
            return `rgb(${r},${g},${b})`;
        }

        // Calculate a point offset perpendicular to a bone's direction.
        // If distPerp > 0, it offsets to the "Right" (Forward side of body if facing right).
        // If distPerp < 0, it offsets to the "Left" (Back side of body).
        function getOffsetPoint(p1, p2, distAlong, distPerp) {
            const dx = p2.x - p1.x;
            const dy = p2.y - p1.y;
            const L = Math.sqrt(dx*dx + dy*dy);
            if (L === 0) return {x: p1.x, y: p1.y};
            const nx = dx / L;
            const ny = dy / L;
            const px = ny;   // Perpendicular vector X
            const py = -nx;  // Perpendicular vector Y
            return {
                x: p1.x + nx * distAlong + px * distPerp,
                y: p1.y + ny * distAlong + py * distPerp
            };
        }

        // --- SVG Drawing Generation ---

        // Draw a single bone (line with thick stroke)
        function drawBone(id, p1, p2, thickness = 22) {
            return `<line id="${id}" x1="${p1.x}" y1="${p1.y}" x2="${p2.x}" y2="${p2.y}" 
                          stroke="#cbd5e1" stroke-width="${thickness}" stroke-linecap="round" />
                    <line x1="${p1.x}" y1="${p1.y}" x2="${p2.x}" y2="${p2.y}" 
                          stroke="#f8fafc" stroke-width="${thickness - 6}" stroke-linecap="round" />
                    <!-- Joint circles -->
                    <circle cx="${p1.x}" cy="${p1.y}" r="${thickness/2 - 2}" fill="#94a3b8" />
                    <circle cx="${p2.x}" cy="${p2.y}" r="${thickness/2 - 2}" fill="#94a3b8" />`;
        }

        // Draw a dynamic muscle belly that bulges based on contraction (0 to 1)
        function drawMuscle(id, origin, insertion, flexAmount, label, alignTextLeft = false) {
            const flexBounded = Math.max(0, Math.min(1, flexAmount));
            const color = interpolateColor(COLOR_RELAXED, COLOR_CONTRACTED, flexBounded);
            
            // Thickness logic: relax = thin (10), contract = thick (35)
            const baseThickness = 12;
            const bulge = baseThickness + flexBounded * 30;
            
            const dx = insertion.x - origin.x;
            const dy = insertion.y - origin.y;
            const L = Math.sqrt(dx*dx + dy*dy);
            const nx = dx / L, ny = dy / L;
            const px = ny, py = -nx;

            const mid = { x: (origin.x + insertion.x)/2, y: (origin.y + insertion.y)/2 };
            
            // Control points for the quadratic curves creating the muscle belly
            const c1 = { x: mid.x + px * bulge, y: mid.y + py * bulge };
            const c2 = { x: mid.x - px * bulge, y: mid.y - py * bulge };

            const pathData = `M ${origin.x} ${origin.y} Q ${c1.x} ${c1.y} ${insertion.x} ${insertion.y} Q ${c2.x} ${c2.y} Z`;
            
            // Text Label Positioning
            const textOffsetDist = alignTextLeft ? -(bulge + 35) : (bulge + 35);
            const textPos = { x: mid.x + px * textOffsetDist, y: mid.y + py * textOffsetDist };

            // Update UI Stats automatically when rendering muscle
            updateStatLabel(`stat-${id}`, flexBounded);

            return `
                <path id="muscle-${id}" d="${pathData}" fill="${color}" class="muscle-path" opacity="0.9" />
                <path d="${pathData}" fill="none" stroke="#7f1d1d" stroke-width="1.5" class="muscle-path" opacity="${0.3 + flexBounded*0.5}"/>
                <circle cx="${origin.x}" cy="${origin.y}" r="4" fill="white" />
                <circle cx="${insertion.x}" cy="${insertion.y}" r="4" fill="white" />
                <!-- Thin Tendon connections (visual only) -->
                <line x1="${origin.x}" y1="${origin.y}" x2="${origin.x - nx*15}" y2="${origin.y - ny*15}" stroke="white" stroke-width="4" stroke-linecap="round" opacity="0.6"/>
                <line x1="${insertion.x}" y1="${insertion.y}" x2="${insertion.x + nx*15}" y2="${insertion.y + ny*15}" stroke="white" stroke-width="4" stroke-linecap="round" opacity="0.6"/>
                
                <!-- Label Background & Text -->
                <g transform="translate(${textPos.x}, ${textPos.y})">
                    <rect x="-40" y="-12" width="80" height="24" rx="12" fill="#1e293b" opacity="0.8" />
                    <text text-anchor="middle" y="4" font-size="12" font-weight="bold" fill="white">${label}</text>
                </g>
            `;
        }

        // --- Render Functions ---

        function renderArm() {
            const angleDeg = state.arm.elbow;
            const flexRatio = angleDeg / 130; // 0 (straight) to 1 (flexed)

            // Skeleton Math
            const Shoulder = { x: 300, y: 150 };
            const Elbow = { x: 300, y: 350 };
            
            // Forearm swings Forward (Right). Angle 0 = straight down.
            // X = sin(angle), Y = cos(angle)
            const Wrist = {
                x: Elbow.x + 200 * Math.sin(angleDeg * deg2rad),
                y: Elbow.y + 200 * Math.cos(angleDeg * deg2rad)
            };

            // Calculate muscle attachment points using the offset helper
            // Humerus is fixed downwards.
            const BicepsOri = getOffsetPoint(Shoulder, Elbow, 20, 20); // Front of shoulder
            const BicepsIns = getOffsetPoint(Elbow, Wrist, 50, 15);   // Front of forearm
            
            const TricepsOri = getOffsetPoint(Shoulder, Elbow, 30, -20); // Back of shoulder
            const TricepsIns = getOffsetPoint(Elbow, Wrist, -20, -15);   // Olecranon (extends past elbow backwards)

            // Triceps insertion correction (Olecranon is essentially rigidly attached to the BACK of the elbow joint)
            // It swings in the OPPOSITE direction of the wrist.
            
            let svgContent = '';
            
            // Draw Scapula (fixed anchor point)
            svgContent += `<path d="M 280 120 L 320 120 L 300 170 Z" fill="#94a3b8" />`;

            // Draw Bones
            svgContent += drawBone('humerus', Shoulder, Elbow);
            svgContent += drawBone('radius-ulna', Elbow, Wrist);
            
            // Hand/Fist simple representation
            svgContent += `<circle cx="${Wrist.x}" cy="${Wrist.y}" r="15" fill="#cbd5e1" />`;

            // Draw Muscles (Antagonistic logic)
            // Biceps contracts when flexing (flexRatio -> 1)
            svgContent += drawMuscle('biceps', BicepsOri, BicepsIns, flexRatio, 'Biceps', false);
            // Triceps contracts when extending (flexRatio -> 0, so 1 - flexRatio -> 1)
            svgContent += drawMuscle('triceps', TricepsOri, TricepsIns, 1 - flexRatio, 'Triceps', true);

            document.getElementById('draw-group').innerHTML = svgContent;
        }

        function renderLeg() {
            const H = state.leg.hip;     // -20 to 90
            const K = state.leg.knee;    // 0 to 130
            const A = state.leg.ankle;   // -30 to 40

            // Skeleton Math - Shifted up by 50px to ensure foot is never cut off
            const Pelvis = { x: 220, y: 100 };
            const Hip = { x: 220, y: 150 };
            
            const femurLen = 150;
            const tibiaLen = 150;
            const footLen = 65;

            // Thigh swings Forward (Right).
            const Knee = {
                x: Hip.x + femurLen * Math.sin(H * deg2rad),
                y: Hip.y + femurLen * Math.cos(H * deg2rad)
            };

            // Calf swings Backward (Left) relative to Thigh.
            const absKneeAngle = H - K;
            const Ankle = {
                x: Knee.x + tibiaLen * Math.sin(absKneeAngle * deg2rad),
                y: Knee.y + tibiaLen * Math.cos(absKneeAngle * deg2rad)
            };

            // Foot angle. Standard position (H=0,K=0,A=0) points RIGHT (90 deg from vertical).
            // Plantar flexion (positive A) points toes DOWN (angle increases).
            const absFootAngle = absKneeAngle + 90 + A;
            const Toe = {
                x: Ankle.x + footLen * Math.sin(absFootAngle * deg2rad),
                y: Ankle.y + footLen * Math.cos(absFootAngle * deg2rad)
            };
            const Heel = {
                x: Ankle.x - 25 * Math.sin(absFootAngle * deg2rad),
                y: Ankle.y - 25 * Math.cos(absFootAngle * deg2rad)
            };

            let svgContent = '';

            // Draw Pelvis/Spine anchor - Shifted up by 50px
            svgContent += `
                <path d="M 200 80 Q 220 100 240 130 L 210 170 Z" fill="#94a3b8" />
                <line x1="220" y1="0" x2="220" y2="100" stroke="#cbd5e1" stroke-width="20" stroke-linecap="round"/>
            `;
            const SpineLower = { x: 220, y: 70 };

            // Draw Bones
            svgContent += drawBone('femur', Hip, Knee, 26);
            svgContent += drawBone('tibia', Knee, Ankle, 22);
            svgContent += drawBone('foot', Heel, Toe, 18);

            // Calculate Flex Ratios for the 3 Leg Pairs
            
            // Pair 4: Hip (Iliopsoas vs Gluteus)
            // Flexor (Iliopsoas) flexes hip (H increases). Extensor (Gluteus) extends (H decreases).
            const hipFlexRatio = (H - (-20)) / 110; // 0 to 1
            const IliopsoasOri = getOffsetPoint(SpineLower, Hip, 30, 25);
            const IliopsoasIns = getOffsetPoint(Hip, Knee, 40, 15);
            const GluteusOri = getOffsetPoint(SpineLower, Hip, 50, -30);
            const GluteusIns = getOffsetPoint(Hip, Knee, 50, -18);
            
            // Pair 2: Knee (Hamstring vs Quadriceps)
            // Flexor (Hamstring) bends knee (K increases). Extensor (Quadriceps) straightens (K decreases).
            const kneeFlexRatio = K / 130;
            const QuadOri = getOffsetPoint(Hip, Knee, 20, 20); // Front thigh
            const QuadIns = getOffsetPoint(Knee, Ankle, 40, 18); // Front shin (via patella)
            const HamOri = getOffsetPoint(Hip, Knee, 20, -18); // Back thigh
            const HamIns = getOffsetPoint(Knee, Ankle, 40, -15); // Back calf

            // Pair 3: Ankle (Tibialis vs Gastrocnemius)
            // Swapped: Tibialis (Flexor) contracts when joint degree increases (A goes towards 40)
            const ankleFlexRatio = (A + 30) / 70; // Tibialis contracts as A goes to 40 (Ratio -> 1)
            const TibialisOri = getOffsetPoint(Knee, Ankle, 30, 18); // Front calf
            const TibialisIns = getOffsetPoint(Ankle, Toe, 35, 12);  // Top of foot
            
            const GastrocOri = getOffsetPoint(Hip, Knee, 140, -18); // Back femur (above knee)
            const GastrocIns = getOffsetPoint(Ankle, Heel, 15, -8); // Heel (calcaneus)

            // Draw Muscles in order (Back to front layering)
            
            // Gluteus & Iliopsoas
            svgContent += drawMuscle('gluteus', GluteusOri, GluteusIns, 1 - hipFlexRatio, 'Gluteus', true);
            svgContent += drawMuscle('iliopsoas', IliopsoasOri, IliopsoasIns, hipFlexRatio, 'Iliopsoas', false);
            
            // Hamstrings & Quadriceps
            svgContent += drawMuscle('hamstrings', HamOri, HamIns, kneeFlexRatio, 'Hamstring', true);
            svgContent += drawMuscle('quadriceps', QuadOri, QuadIns, 1 - kneeFlexRatio, 'Quadriceps', false);
            
            // Patella (knee cap) visual over the quad tendon
            const PatellaPos = getOffsetPoint(Knee, Ankle, 5, 20);
            svgContent += `<circle cx="${PatellaPos.x}" cy="${PatellaPos.y}" r="6" fill="#cbd5e1" stroke="white" stroke-width="2"/>`;

            // Gastrocnemius & Tibialis Anterior
            svgContent += drawMuscle('gastrocnemius', GastrocOri, GastrocIns, 1 - ankleFlexRatio, 'Gastroc.', true);
            svgContent += drawMuscle('tibialis', TibialisOri, TibialisIns, ankleFlexRatio, 'Tibialis Ant.', false);

            document.getElementById('draw-group').innerHTML = svgContent;
        }


        // --- UI & Interaction ---

        function updateStatLabel(elementId, flexRatio) {
            const el = document.getElementById(elementId);
            if(!el) return;
            
            if(flexRatio > 0.6) {
                el.textContent = "Contracting";
                el.className = "text-xs font-bold px-2 py-1 rounded bg-red-100 text-red-700 border border-red-200";
            } else if (flexRatio < 0.4) {
                el.textContent = "Relaxing";
                el.className = "text-xs font-bold px-2 py-1 rounded bg-slate-200 text-slate-600 border border-slate-300";
            } else {
                el.textContent = "Neutral";
                el.className = "text-xs font-bold px-2 py-1 rounded bg-blue-100 text-blue-700 border border-blue-200";
            }
        }

        function updateState(limb, joint, value) {
            const valNum = parseFloat(value);
            state[limb][joint] = valNum;
            document.getElementById(`val-${joint}`).textContent = valNum + '°';
            
            if(limb === 'arm') {
                renderArm();
            } else {
                renderLeg();
            }
        }

        function switchTab(target) {
            state.activeTab = target;
            
            // Update Buttons
            document.getElementById('tab-arm').className = `tab-btn px-6 py-2 rounded-full border font-semibold focus:outline-none ${target === 'arm' ? 'active border-blue-600' : 'bg-white border-slate-300 text-slate-600'}`;
            document.getElementById('tab-leg').className = `tab-btn px-6 py-2 rounded-full border font-semibold focus:outline-none ${target === 'leg' ? 'active border-blue-600' : 'bg-white border-slate-300 text-slate-600'}`;
            
            // Toggle Controls panels
            if(target === 'arm') {
                document.getElementById('controls-arm').classList.remove('hidden');
                document.getElementById('controls-leg').classList.add('hidden');
                document.getElementById('canvas').setAttribute('viewBox', '0 0 600 700'); // Standard viewBox for Arm
                renderArm();
            } else {
                document.getElementById('controls-arm').classList.add('hidden');
                document.getElementById('controls-leg').classList.remove('hidden');
                document.getElementById('canvas').setAttribute('viewBox', '0 -20 650 850'); // Enlarged viewBox for Leg
                renderLeg();
            }
        }

        function animateSlider(sliderId, targetValue) {
            const slider = document.getElementById(sliderId);
            const startVal = parseFloat(slider.value);
            const diff = targetValue - startVal;
            const duration = 400; // ms
            const startTime = performance.now();

            function step(currentTime) {
                const elapsed = currentTime - startTime;
                let progress = elapsed / duration;
                if(progress > 1) progress = 1;
                
                // Ease out quad
                const ease = progress * (2 - progress);
                const currentVal = startVal + (diff * ease);
                
                slider.value = currentVal;
                
                // Manually trigger the update function
                const limb = sliderId.includes('elbow') ? 'arm' : 'leg';
                const joint = sliderId.replace('slider-', '');
                updateState(limb, joint, currentVal);

                if(progress < 1) {
                    requestAnimationFrame(step);
                }
            }
            requestAnimationFrame(step);
        }

        // Initialization
        window.onload = () => {
            renderArm(); // Default view
        };

    </script>
</body>
</html>
