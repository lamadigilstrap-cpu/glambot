# glambot
hair salon assistant 
import OpenAI from "openai";
import { NextResponse } from "next/server";

const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY,
});

export async function POST(req: Request) {
  const { message } = await req.json();

  const completion = await openai.chat.completions.create({
    model: "gpt-4o-mini",
    messages: [
      {
        role: "system",
        content: `
You are a professional hair salon assistant.
You help clients with:
- Hairstyle recommendations
- Hair care advice
- Color suggestions
- Appointment booking
Be friendly, stylish, and concise.
        `,
      },
      { role: "user", content: message },
    ],
  });

  return NextResponse.json({
    reply: completion.choices[0].message.content,
  });
}
"use client";
import { useState } from "react";

export default function ChatBox() {
  const [input, setInput] = useState("");
  const [messages, setMessages] = useState<string[]>([]);

  async function sendMessage() {
    const res = await fetch("/api/chat", {
      method: "POST",
      body: JSON.stringify({ message: input }),
    });

    const data = await res.json();
    setMessages([...messages, "You: " + input, "Assistant: " + data.reply]);
    setInput("");
  }

  return (
    <div className="max-w-xl mx-auto mt-10 p-4 border rounded">
      <h1 className="text-2xl font-bold mb-4">💇 AI Hair Salon Assistant</h1>

      <div className="h-64 overflow-y-auto mb-4">
        {messages.map((msg, i) => (
          <p key={i} className="mb-2">{msg}</p>
        ))}
      </div>

      <input
        className="border p-2 w-full"
        value={input}
        onChange={(e) => setInput(e.target.value)}
        placeholder="Ask about hairstyles, color, booking..."
      />

      <button
        onClick={sendMessage}
        className="bg-black text-white px-4 py-2 mt-2 w-full"
      >
        Send
      </button>
    </div>
  );
}
import ChatBox from "@/components/ChatBox";

export default function Home() {
  return (
    <main className="min-h-screen bg-gray-100">
      <ChatBox />
    </main>
  );
}
# 💇 AI Hair Salon Assistant

An AI-powered salon assistant that recommends hairstyles,
answers hair care questions, and helps with bookings.

## Features
- Hairstyle & color advice
- Hair care tips
- Friendly salon personality
- Deployable in minutes

## Setup
```bash
npm install
npm run dev
