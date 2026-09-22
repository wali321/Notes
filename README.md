# Competition Cheatsheet — shadcn/ui + WebGL (Three.js)

> Only the parts you said you need.  
> Stack: Next.js App Router + TypeScript + Tailwind + shadcn/ui + Three.js

────────────────────────────────────────────────────────

1. SETUP (do this once before competition)

npx create-next-app@latest . --typescript --tailwind --eslint --app --src-dir --import-alias "@/*"
npx shadcn@latest init
npm install three @types/three

Add the components you’ll actually use:

npx shadcn@latest add button card input textarea label badge separator
npx shadcn@latest add navigation-menu sheet dialog form
npx shadcn@latest add accordion tabs avatar
npx shadcn@latest add sonner
npx shadcn@latest add skeleton

────────────────────────────────────────────────────────

2. SHADCN QUICK PATTERNS

Button variants:

import { Button } from "@/components/ui/button"

<Button>Default</Button>
<Button variant="secondary">Secondary</Button>
<Button variant="outline">Outline</Button>
<Button variant="ghost">Ghost</Button>
<Button variant="destructive">Delete</Button>
<Button size="lg">Large CTA</Button>
<Button size="sm">Small</Button>
<Button size="icon"><Icon /></Button>


Card:

import { Card, CardHeader, CardTitle, CardDescription, CardContent, CardFooter } from "@/components/ui/card"
import { Button } from "@/components/ui/button"

<Card className="hover:shadow-lg transition-shadow">
  <CardHeader>
    <CardTitle>Feature title</CardTitle>
    <CardDescription>Short benefit line</CardDescription>
  </CardHeader>
  <CardContent>
    <p>Extra content if needed</p>
  </CardContent>
  <CardFooter>
    <Button>Learn more</Button>
  </CardFooter>
</Card>


Responsive card grid:

<div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
  {items.map(...)}
</div>


Contact Form:

"use client"
import { useState } from "react"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Textarea } from "@/components/ui/textarea"
import { Label } from "@/components/ui/label"
import { toast } from "sonner"

export function ContactForm() {
  const [loading, setLoading] = useState(false)

  async function onSubmit(e: React.FormEvent) {
    e.preventDefault()
    setLoading(true)
    await new Promise(r => setTimeout(r, 800))
    toast.success("Message sent!")
    setLoading(false)
  }

  return (
    <form onSubmit={onSubmit} className="space-y-4 max-w-md">
      <div className="space-y-2">
        <Label htmlFor="name">Name</Label>
        <Input id="name" required />
      </div>
      <div className="space-y-2">
        <Label htmlFor="email">Email</Label>
        <Input id="email" type="email" required />
      </div>
      <div className="space-y-2">
        <Label htmlFor="message">Message</Label>
        <Textarea id="message" required rows={4} />
      </div>
      <Button type="submit" disabled={loading} className="w-full">
        {loading ? "Sending..." : "Send Message"}
      </Button>
    </form>
  )
}


In layout.tsx add:

import { Toaster } from "@/components/ui/sonner"

<body>
  {children}
  <Toaster />
</body>


Mobile Nav (Sheet):

"use client"
import { Sheet, SheetContent, SheetTrigger } from "@/components/ui/sheet"
import { Button } from "@/components/ui/button"
import { Menu } from "lucide-react"
import Link from "next/link"

export function MobileNav() {
  return (
    <Sheet>
      <SheetTrigger asChild>
        <Button variant="ghost" size="icon" className="md:hidden">
          <Menu />
        </Button>
      </SheetTrigger>
      <SheetContent side="right">
        <nav className="flex flex-col gap-4 mt-8">
          <Link href="#features">Features</Link>
          <Link href="#about">About</Link>
          <Link href="#contact">Contact</Link>
          <Button asChild>
            <Link href="#cta">Get Started</Link>
          </Button>
        </nav>
      </SheetContent>
    </Sheet>
  )
}


Accordion (FAQ):

import { Accordion, AccordionContent, AccordionItem, AccordionTrigger } from "@/components/ui/accordion"

<Accordion type="single" collapsible className="w-full">
  <AccordionItem value="item-1">
    <AccordionTrigger>Question one?</AccordionTrigger>
    <AccordionContent>Answer here.</AccordionContent>
  </AccordionItem>
</Accordion>

────────────────────────────────────────────────────────

3. WEBGL / THREE.JS

Rule #1
Never put Three.js in a Server Component.
Always: "use client" + dynamic(..., { ssr: false })

Folder structure:

src/components/three/
├── CanvasWrapper.tsx
└── Scene.tsx


CanvasWrapper.tsx

"use client"
import dynamic from "next/dynamic"

const Scene = dynamic(() => import("./Scene"), {
  ssr: false,
  loading: () => (
    <div className="h-full w-full rounded-xl bg-muted animate-pulse" />
  ),
})

export default function CanvasWrapper({ className }: { className?: string }) {
  return (
    <div className={className ?? "h-[400px] w-full lg:h-[500px]"}>
      <Scene />
    </div>
  )
}


Scene.tsx (minimal spinning object)

"use client"
import { useEffect, useRef } from "react"
import * as THREE from "three"

export default function Scene() {
  const mountRef = useRef<HTMLDivElement>(null)

  useEffect(() => {
    if (!mountRef.current) return

    const width = mountRef.current.clientWidth
    const height = mountRef.current.clientHeight

    const scene = new THREE.Scene()

    const camera = new THREE.PerspectiveCamera(50, width / height, 0.1, 100)
    camera.position.z = 4

    const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true })
    renderer.setSize(width, height)
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
    mountRef.current.appendChild(renderer.domElement)

    scene.add(new THREE.AmbientLight(0xffffff, 0.6))
    const dir = new THREE.DirectionalLight(0xffffff, 1.2)
    dir.position.set(5, 5, 5)
    scene.add(dir)

    const geometry = new THREE.IcosahedronGeometry(1.3, 1)
    // alternatives:
    // const geometry = new THREE.TorusKnotGeometry(0.9, 0.3, 100, 16)
    // const geometry = new THREE.SphereGeometry(1.2, 32, 32)

    const material = new THREE.MeshStandardMaterial({
      color: "#6366f1",
      metalness: 0.25,
      roughness: 0.4,
      flatShading: true,
    })
    const mesh = new THREE.Mesh(geometry, material)
    scene.add(mesh)

    let frameId: number
    const animate = () => {
      mesh.rotation.x += 0.004
      mesh.rotation.y += 0.007
      renderer.render(scene, camera)
      frameId = requestAnimationFrame(animate)
    }
    animate()

    const onResize = () => {
      if (!mountRef.current) return
      const w = mountRef.current.clientWidth
      const h = mountRef.current.clientHeight
      camera.aspect = w / h
      camera.updateProjectionMatrix()
      renderer.setSize(w, h)
    }
    window.addEventListener("resize", onResize)

    return () => {
      cancelAnimationFrame(frameId)
      window.removeEventListener("resize", onResize)
      geometry.dispose()
      material.dispose()
      renderer.dispose()
      if (mountRef.current?.contains(renderer.domElement)) {
        mountRef.current.removeChild(renderer.domElement)
      }
    }
  }, [])

  return <div ref={mountRef} className="h-full w-full" />
}


Usage:

import CanvasWrapper from "@/components/three/CanvasWrapper"

<section>
  <div className="grid lg:grid-cols-2 gap-12 items-center">
    <div>{/* text + Button */}</div>
    <CanvasWrapper className="h-[420px] rounded-2xl overflow-hidden" />
  </div>
</section>

────────────────────────────────────────────────────────

4. COOL VARIATIONS

A. Mouse parallax (add inside useEffect after creating mesh)

const onMouseMove = (e: MouseEvent) => {
  const x = (e.clientX / window.innerWidth) * 2 - 1
  const y = -(e.clientY / window.innerHeight) * 2 + 1
  mesh.rotation.y = x * 0.4
  mesh.rotation.x = y * 0.3
}
window.addEventListener("mousemove", onMouseMove)

// in cleanup:
window.removeEventListener("mousemove", onMouseMove)


B. Wireframe look

const material = new THREE.MeshBasicMaterial({
  color: "#a5b4fc",
  wireframe: true,
})


C. Multiple objects

const group = new THREE.Group()
for (let i = 0; i < 5; i++) {
  const geo = new THREE.IcosahedronGeometry(0.4, 0)
  const mat = new THREE.MeshStandardMaterial({ color: "#818cf8", flatShading: true })
  const m = new THREE.Mesh(geo, mat)
  m.position.set((Math.random() - 0.5) * 4, (Math.random() - 0.5) * 3, (Math.random() - 0.5) * 2)
  group.add(m)
}
scene.add(group)

// in animate:
group.rotation.y += 0.003


D. Load .glb model

import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js"

const loader = new GLTFLoader()
loader.load("/models/product.glb", (gltf) => {
  const model = gltf.scene
  model.scale.setScalar(1.8)
  scene.add(model)
})

────────────────────────────────────────────────────────

5. PERFORMANCE RULES

- dynamic(..., { ssr: false })          → prevents hydration crash
- setPixelRatio(Math.min(dpr, 2))       → stops high-DPI phones from melting
- Always dispose geometry + material + renderer
- Reserve height on the container       → no layout shift
- Prefer low-poly (IcosahedronGeometry with detail 1)
- Don’t put heavy 3D above the fold on mobile if Lighthouse is strict

────────────────────────────────────────────────────────

6. COMPETITION DAY FLOW

1. Scaffold + shadcn init + add Button/Card/Input/Sheet
2. Build full page with shadcn components
3. Drop CanvasWrapper into Hero or Features
4. Tweak mesh color to match primary
5. Optional: mouse parallax or wireframe
6. Final 10 min → mobile check + dispose cleanup
