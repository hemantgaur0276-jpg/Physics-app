import os, zipfile, json, textwrap
root="/mnt/data/Magnetism_GaussLaw_AllThree_Kit"
web=f"{root}/website"; app=f"{root}/android-app"
os.makedirs(web,exist_ok=True)
os.makedirs(f"{app}/app/src/main/assets",exist_ok=True)
os.makedirs(f"{app}/app/src/main/java/com/example/physicsstudy",exist_ok=True)
os.makedirs(f"{root}/.github/workflows",exist_ok=True)

questions=[
("Electric flux through a flat surface is","EA cosθ","EA sinθ","E/A","A/E",0),
("SI unit of electric flux is","N m²/C","N/C","C/m²","V/m",0),
("Gauss's law relates net flux to","Enclosed net charge","Outside charge","Area only","Potential only",0),
("A Gaussian surface must be","Closed","Spherical","Flat","Conducting",0),
("Outside charges contribute what net flux through a closed surface?","Zero","Always positive","Always negative","Infinite",0),
("A point charge is best enclosed by a","Concentric sphere","Open plane","Cylinder only","Cone",0),
("An infinite line charge uses a","Coaxial cylinder","Sphere only","Pillbox","Cube only",0),
("An infinite plane sheet uses a","Pillbox","Torus","Sphere","Cone",0),
("Field of an infinite line charge varies as","1/r","1/r²","r²","Constant",0),
("Field of an ideal infinite sheet depends on","Surface charge density","Distance squared","Inverse distance","Mass",0),
("Magnetic force on a moving charge is","q(v × B)","qE","q(v · B)","mB",0),
("Magnetic force is zero when velocity is","Parallel to B","Perpendicular to B","At 45° to B","Never",0),
("Magnetic force does what work?","Zero work","Positive work always","Negative work always","Infinite work",0),
("For v perpendicular to uniform B, path is","Circular","Straight","Parabolic","Random",0),
("Radius of circular motion in B is","mv/(|q|B)","|q|B/mv","mB/qv","qv/mB",0),
("Biot–Savart law gives","Magnetic field from current element","Electric potential","Resistance","Electric flux",0),
("Biot–Savart direction uses","Right-hand rule","Ohm's law","Lenz's law","Gauss's law",0),
("Ampere's law uses current","Enclosed by loop","Outside only","In a distant battery","Total universe",0),
("Field near a long straight wire varies as","1/r","r","1/r²","r²",0),
("Parallel currents in same direction","Attract","Repel","No force","Cancel",0),
("Parallel currents in opposite directions","Repel","Attract","No force","Merge",0),
("Force on straight current wire is","ILB sinθ","IB/L","I/(LB)","LB/I",0),
("Magnetic moment of a current loop is","NIA","NI/A","A/NI","NB/I",0),
("Torque on magnetic dipole is","μB sinθ","μ/B","B/μ","μB cosθ always",0),
("Magnetic field lines form","Closed loops","Open ends","Only straight lines","Random paths",0),
("SI unit of magnetic field is","tesla","weber","ampere","coulomb",0),
("SI unit of magnetic flux is","weber","tesla","newton","ohm",0),
("Magnetic force angle is between","v and B","q and m","E and mass","I and charge",0),
("A stationary charge in B feels magnetic force","Zero","qB","qE","Infinite",0),
("Ideal long solenoid field inside is","μ₀nI","μ₀I/2πr","μ₀I/4πr²","n/μ₀I",0),
("Ideal very long solenoid field outside is approximately","Zero","Infinite","μ₀nI","Same as inside",0),
("Cyclotron period is","2πm/(|q|B)","mv/qB","qB/2πm","2πqB/m",0),
("If speed doubles in perpendicular B, radius","Doubles","Halves","Unchanged","Quadruples",0),
("If B doubles at same speed, radius","Halves","Doubles","Unchanged","Quadruples",0),
("Positive charge force direction follows","Right-hand rule for v × B","v + B","Dot product","Gauss's law",0),
("Negative charge force is","Opposite to v × B","Same as v × B","Always upward","Always zero",0),
("A magnetic dipole in uniform B can experience","Torque","Always net force","Electric flux","No torque",0),
("At 90° between μ and B, torque is","Maximum","Zero","Undefined","Minimum",0),
("At 0° between μ and B, torque is","Zero","Maximum","Infinite","μ/B",0),
("ε₀ means","Permittivity of free space","Permeability","Resistance","Conductivity",0),
("μ₀ means","Permeability of free space","Permittivity","Charge density","Potential",0),
("Linear charge density means","Charge per unit length","Per area","Per volume","Current density",0),
("Surface charge density means","Charge per unit area","Per length","Per volume","Energy per area",0),
("Infinite charged sheet field is","Normal to sheet","Along sheet","Circular","Zero",0),
("On a sphere around point charge, E magnitude is","Constant","Zero","Tangential","Variable",0),
("E·dA is maximum when E is","Parallel to area normal","Perpendicular to normal","At 90°","Zero",0),
("Long-wire magnetic field is","μ₀I/(2πr)","μ₀Ir/2π","μ₀I/(4πr²)","2πr/μ₀I",0),
("Force per length between wires varies with distance as","1/d","d","d²","Independent",0),
("Gaussian surface is chosen to exploit","Symmetry","Colour","Cost","Temperature",0),
("Gauss's law applies to","Any closed surface","Only spheres","Only conductors","Only vacuum",0),
("Magnetic monopoles in ordinary classical electromagnetism are","Not observed","Isolated north charges","Required","Electrons",0)
]
qjson=json.dumps(questions)
html='''<!doctype html><html><head><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1"><title>Physics Study Kit</title>
<style>
body{margin:0;background:#f3f6fb;color:#172334;font:16px/1.55 system-ui}header{background:#10243a;color:white;padding:25px}main{max-width:950px;margin:auto;padding:16px}nav{display:flex;gap:8px;flex-wrap:wrap;padding:14px;max-width:950px;margin:auto}button{border:0;border-radius:9px;padding:10px 14px;cursor:pointer;background:#e4ebf5;color:#10243a;font-weight:650}nav button.active,.primary{background:#2463eb;color:white}.panel{display:none}.panel.active{display:block}.card{background:white;border:1px solid #dce4ef;border-radius:14px;padding:16px;margin:12px 0}.grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(230px,1fr));gap:12px}.formula{background:#eef4ff;padding:10px;border-left:4px solid #2463eb;border-radius:6px;font-family:monospace;overflow-wrap:anywhere}.option{display:block;width:100%;text-align:left;margin:6px 0;background:white;border:1px solid #dce4ef}.correct{background:#e7f8ee;border-color:green}.wrong{background:#fff0f0;border-color:red}.muted{color:#66758a;font-size:.9rem}
</style></head><body><header><h1>Magnetism &amp; Gauss's Law</h1><p>Physics Study Kit · Notes · Formula sheet · 50-question quiz</p></header>
<nav><button class="active" data-tab="notes">Notes</button><button data-tab="formulas">Formula sheet</button><button data-tab="quiz">50-question quiz</button></nav><main>
<section id="notes" class="panel active"><h2>Chapter notes</h2>
<div class="card"><h3>Electric flux &amp; Gauss's law</h3><p>Electric flux measures the electric field passing through a surface. For uniform field over a flat area:</p><div class="formula">ΦE = EA cosθ</div><p>Net electric flux through any closed surface equals enclosed net charge divided by free-space permittivity.</p><div class="formula">∮ E · dA = Qenc / ε₀</div><ul><li>Only net enclosed charge determines total flux.</li><li>External charges may create field on the surface but contribute zero net flux.</li><li>Choose a Gaussian surface that matches charge symmetry.</li></ul></div>
<div class="grid"><div class="card"><h3>Spherical symmetry</h3><p>Use a concentric sphere for a point charge or spherically symmetric distribution.</p><div class="formula">E(4πr²) = Qenc/ε₀</div></div><div class="card"><h3>Infinite line charge</h3><p>Use a coaxial cylinder; flux through end caps is zero.</p><div class="formula">E(2πrL) = λL/ε₀<br>E = λ/(2πε₀r)</div></div><div class="card"><h3>Infinite plane sheet</h3><p>Use a pillbox; flux passes through both flat faces.</p><div class="formula">2EA = σA/ε₀<br>E = σ/(2ε₀)</div></div></div>
<div class="card"><h3>Magnetic force on a moving charge</h3><p>Magnetic force is perpendicular to velocity and field. It changes direction, not speed, in a magnetic field alone.</p><div class="formula">F = q(v × B) ; |F| = |q|vB sinθ</div><p>For v perpendicular to uniform B:</p><div class="formula">r = mv/(|q|B) ; T = 2πm/(|q|B)</div></div>
<div class="grid"><div class="card"><h3>Biot–Savart law</h3><p>Magnetic field contribution from a current element:</p><div class="formula">dB = (μ₀/4π) I(dl × r̂)/r²</div></div><div class="card"><h3>Ampere's circuital law</h3><p>For steady currents in vacuum, circulation of B around a closed path:</p><div class="formula">∮ B · dl = μ₀ Ienc</div></div><div class="card"><h3>Force on current wire</h3><div class="formula">F = I(L × B) ; |F| = ILB sinθ</div></div><div class="card"><h3>Parallel wires</h3><p>Same-direction currents attract; opposite-direction currents repel.</p><div class="formula">F/L = μ₀I₁I₂/(2πd)</div></div></div>
<div class="card"><h3>Magnetic dipole</h3><p>A current loop behaves as a magnetic dipole.</p><div class="formula">μ = NIA ; τ = μB sinθ</div><p>Magnetic field lines form closed loops; isolated magnetic monopoles have not been observed.</p></div>
<p class="muted">General Class 12-level starter notes. Check your board's prescribed syllabus and textbook for exact scope and derivations.</p></section>
<section id="formulas" class="panel"><h2>Quick formula sheet</h2><div class="grid">
<div class="card"><h3>Electric flux / Gauss</h3><div class="formula">ΦE = EA cosθ</div><div class="formula">∮E·dA = Qenc/ε₀</div><div class="formula">Point charge: E = q/(4πε₀r²)</div><div class="formula">Line: E = λ/(2πε₀r)</div><div class="formula">Sheet: E = σ/(2ε₀)</div></div>
<div class="card"><h3>Magnetic force</h3><div class="formula">F = q(v × B)</div><div class="formula">F = |q|vB sinθ</div><div class="formula">r = mv/(|q|B)</div><div class="formula">T = 2πm/(|q|B)</div><div class="formula">Fwire = ILB sinθ</div></div>
<div class="card"><h3>Magnetic field laws</h3><div class="formula">dB = (μ₀/4π)I(dl × r̂)/r²</div><div class="formula">∮B·dl = μ₀Ienc</div><div class="formula">Long wire: B = μ₀I/(2πr)</div><div class="formula">Solenoid: B = μ₀nI</div></div>
<div class="card"><h3>Dipole / wires</h3><div class="formula">μ = NIA</div><div class="formula">τ = μB sinθ</div><div class="formula">F/L = μ₀I₁I₂/(2πd)</div></div></div><p class="muted">ε₀: permittivity; μ₀: permeability; λ: linear charge density; σ: surface charge density.</p></section>
<section id="quiz" class="panel"><h2>50-question practice quiz</h2><p class="muted">Choose one option per question. Correct answers are shown after each selection.</p><div id="questions"></div><p id="score">Answered: 0/50 · Score: 0</p><button class="primary" id="reset">Restart quiz</button></section>
</main><script>
const qs=QUESTIONS;let picks={};function draw(){let box=document.getElementById('questions');box.innerHTML='';qs.forEach((q,i)=>{let c=document.createElement('div');c.className='card';let t=document.createElement('b');t.textContent=(i+1)+'. '+q[0];c.append(t);q.slice(1,5).forEach((a,j)=>{let b=document.createElement('button');b.className='option';b.textContent='ABCD'[j]+'. '+a;b.disabled=picks[i]!==undefined;if(picks[i]!==undefined){if(j===q[5])b.classList.add('correct');if(j===picks[i]&&j!==q[5])b.classList.add('wrong')}b.onclick=()=>{picks[i]=j;draw()};c.append(b)});box.append(c)});let n=Object.keys(picks).length,s=Object.keys(picks).filter(i=>picks[i]===qs[i][5]).length;document.getElementById('score').textContent=`Answered: ${n}/50 · Score: ${s}`;}
document.querySelectorAll('nav button').forEach(b=>b.onclick=()=>{document.querySelectorAll('nav button').forEach(x=>x.classList.remove('active'));document.querySelectorAll('.panel').forEach(x=>x.classList.remove('active'));b.classList.add('active');document.getElementById(b.dataset.tab).classList.add('active')});document.getElementById('reset').onclick=()=>{picks={};draw()};draw();
</script></body></html>'''.replace("QUESTIONS",qjson)
open(f"{web}/index.html","w").write(html)
open(f"{app}/app/src/main/assets/index.html","w").write(html)

contents={
"settings.gradle.kts":'pluginManagement { repositories { google(); mavenCentral(); gradlePluginPortal() } }\ndependencyResolutionManagement { repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS); repositories { google(); mavenCentral() } }\nrootProject.name = "PhysicsStudyKit"\ninclude(":app")\n',
"build.gradle.kts":'plugins { id("com.android.application") version "8.7.3" apply false; id("org.jetbrains.kotlin.android") version "2.0.21" apply false }\n',
"gradle.properties":"org.gradle.jvmargs=-Xmx2048m -Dfile.encoding=UTF-8\nandroid.useAndroidX=true\n",
"app/build.gradle.kts":'plugins { id("com.android.application"); id("org.jetbrains.kotlin.android") }\nandroid { namespace = "com.example.physicsstudy"; compileSdk = 35; defaultConfig { applicationId = "com.example.physicsstudy"; minSdk = 23; targetSdk = 35; versionCode = 1; versionName = "1.0" } }\n',
"app/src/main/AndroidManifest.xml":'<?xml version="1.0" encoding="utf-8"?><manifest xmlns:android="http://schemas.android.com/apk/res/android"><application android:theme="@android:style/Theme.Material.Light.NoActionBar" android:label="Physics Study Kit"><activity android:name=".MainActivity" android:exported="true"><intent-filter><action android:name="android.intent.action.MAIN"/><category android:name="android.intent.category.LAUNCHER"/></intent-filter></activity></application></manifest>\n',
"app/src/main/java/com/example/physicsstudy/MainActivity.kt":'''package com.example.physicsstudy
import android.app.Activity
import android.os.Bundle
import android.webkit.WebView
import android.webkit.WebViewClient
class MainActivity : Activity() {
 override fun onCreate(savedInstanceState: Bundle?) {
  super.onCreate(savedInstanceState)
  val view = WebView(this)
  view.webViewClient = WebViewClient()
  view.settings.javaScriptEnabled = true
  view.settings.domStorageEnabled = true
  setContentView(view)
  view.loadUrl("file:///android_asset/index.html")
 }
}
'''
}
for rel,data in contents.items():
 p=f"{app}/{rel}";os.makedirs(os.path.dirname(p),exist_ok=True);open(p,"w").write(data)

workflow='''name: Build Physics Study Kit APK
on:
  workflow_dispatch:
  push:
    branches: [ main ]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'
      - uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: '8.9'
      - uses: android-actions/setup-android@v3
      - run: sdkmanager "platforms;android-35" "build-tools;35.0.0"
      - run: gradle --no-daemon assembleDebug
        working-directory: android-app
      - uses: actions/upload-artifact@v4
        with:
          name: PhysicsStudyKit-debug-apk
          path: android-app/app/build/outputs/apk/debug/app-debug.apk
'''
open(f"{root}/.github/workflows/build-apk.yml","w").write(workflow)
readme="""# Magnetism & Gauss's Law — All-in-one Study Kit

Contains:
- `website/index.html`: mobile-friendly study website with notes, formulas and 50 interactive MCQs.
- `android-app/`: Android app project that packages the same material locally.
- `.github/workflows/build-apk.yml`: cloud workflow to compile the Android APK.

## Website
Open `website/index.html` in a browser. To publish online, upload this project to GitHub and enable GitHub Pages for the `website` folder (or publish its contents).

## Build APK using only an Android phone
1. Extract this ZIP.
2. Create a **private** GitHub repository.
3. Upload all package contents to the repository root, preserving folder structure (including `.github/workflows`).
4. Open the repository's Actions tab and run **Build Physics Study Kit APK**.
5. After success, download the `PhysicsStudyKit-debug-apk` artifact and extract it.
6. Install `app-debug.apk` on your phone. Review the source before installing.

The APK is not precompiled here; GitHub Actions compiles it in the cloud.

## In-chat study
The accompanying ChatGPT conversation can include interactive recall cards for quick revision.

## Scope
General Class 12-level starter material; check your board's prescribed syllabus and textbook for exact coverage.
"""
open(f"{root}/README.md","w").write(readme)
zpath="/mnt/data/Magnetism_GaussLaw_AllThree_Kit.zip"
with zipfile.ZipFile(zpath,"w",zipfile.ZIP_DEFLATED) as z:
 for folder,dirs,names in os.walk(root):
  for name in names:
   path=os.path.join(folder,name);z.write(path,os.path.relpath(path,root))
print(zpath, "created")
# Physics-app
