# numerology
import React, { useMemo, useState } from "react";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Tabs, TabsList, TabsTrigger, TabsContent } from "@/components/ui/tabs";
import { Badge } from "@/components/ui/badge";
import { Separator } from "@/components/ui/separator";
import { CalendarDays, Sparkles, HeartHandshake, BarChart3, UserCircle2, Clock3, Star, ChevronRight } from "lucide-react";
import { motion } from "framer-motion";

const MONTH_NAMES = [
  "January","February","March","April","May","June",
  "July","August","September","October","November","December"
];

const CORE_NUMBERS = {
  1: {
    archetype: "Initiator",
    keywords: ["leadership", "independence", "clarity"],
    strengths: "Best for bold starts, direct action, and choosing your own lane.",
    caution: "Avoid forcing timing or carrying everything alone.",
    love: "You need honesty, momentum, and clear intentions.",
    career: "Great for launches, applications, pitches, and personal branding.",
  },
  2: {
    archetype: "Harmonizer",
    keywords: ["partnership", "patience", "sensitivity"],
    strengths: "Best for collaboration, healing, diplomacy, and refining plans.",
    caution: "Avoid passivity, overthinking, or outsourcing your voice.",
    love: "Relationships deepen through softness and consistent communication.",
    career: "Excellent for partnerships, client work, and negotiation.",
  },
  3: {
    archetype: "Expression",
    keywords: ["creativity", "joy", "communication"],
    strengths: "Best for visibility, writing, content, art, and social momentum.",
    caution: "Avoid scattering your energy or confusing motion with progress.",
    love: "Charm rises when you speak directly and keep things playful.",
    career: "Strong for publishing, speaking, marketing, and storytelling.",
  },
  4: {
    archetype: "Builder",
    keywords: ["discipline", "systems", "stability"],
    strengths: "Best for structure, consistency, budgeting, and long-term foundations.",
    caution: "Avoid rigidity, pessimism, or doing work that no longer matters.",
    love: "Trust grows through reliability and practical support.",
    career: "Great for planning, operations, documentation, and process improvement.",
  },
  5: {
    archetype: "Catalyst",
    keywords: ["change", "freedom", "adaptability"],
    strengths: "Best for reinvention, travel, experimentation, and brave pivots.",
    caution: "Avoid impulsive decisions or escaping before learning the lesson.",
    love: "You need oxygen, novelty, and emotional honesty.",
    career: "Strong for outreach, transitions, testing, and dynamic environments.",
  },
  6: {
    archetype: "Nurturer",
    keywords: ["care", "responsibility", "beauty"],
    strengths: "Best for relationships, family, healing, design, and devotion.",
    caution: "Avoid rescuing people or measuring your worth through usefulness.",
    love: "Commitment deepens when care is mutual rather than one-sided.",
    career: "Great for service, client care, teaching, wellness, and home-related projects.",
  },
  7: {
    archetype: "Seeker",
    keywords: ["truth", "introspection", "wisdom"],
    strengths: "Best for research, study, reflection, and strategic withdrawal.",
    caution: "Avoid isolation, cynicism, or waiting for perfect certainty.",
    love: "You need trust, depth, and emotional intelligence.",
    career: "Strong for analysis, writing, spiritual work, science, and investigation.",
  },
  8: {
    archetype: "Powerhouse",
    keywords: ["mastery", "money", "influence"],
    strengths: "Best for ambition, leadership, scaling, and financial growth.",
    caution: "Avoid control battles or chasing status without alignment.",
    love: "Respect and shared vision matter more than empty chemistry.",
    career: "Great for promotions, negotiations, entrepreneurship, and assets.",
  },
  9: {
    archetype: "Completion",
    keywords: ["release", "compassion", "closure"],
    strengths: "Best for endings, forgiveness, teaching, and perspective.",
    caution: "Avoid clinging to expired roles, stories, or relationships.",
    love: "Healing comes from letting old patterns end cleanly.",
    career: "Strong for finishing work, public impact, mentoring, and legacy thinking.",
  },
};

function reduceNumber(num: number, keepMasters = true): number {
  while (num > 9) {
    if (keepMasters && (num === 11 || num === 22 || num === 33)) return num;
    num = String(num)
      .split("")
      .reduce((a, b) => a + Number(b), 0);
  }
  return num;
}

function sumDigits(value: string) {
  return value.replace(/\D/g, "").split("").reduce((a, b) => a + Number(b || 0), 0);
}

function lifePathFromDob(dob: string) {
  if (!dob) return 0;
  const digits = dob.replace(/\D/g, "");
  if (digits.length !== 8) return 0;
  return reduceNumber(sumDigits(dob), true);
}

function personalYearNumber(dob: string, month: number, year: number) {
  if (!dob) return 0;
  const [y, m, d] = dob.split("-").map(Number);
  if (!y || !m || !d) return 0;
  return reduceNumber(reduceNumber(m, false) + reduceNumber(d, false) + reduceNumber(year, false), true);
}

function personalMonthNumber(personalYear: number, month: number) {
  if (!personalYear) return 0;
  return reduceNumber(personalYear + reduceNumber(month + 1, false), false);
}

function personalDayNumber(personalMonth: number, date: number) {
  if (!personalMonth) return 0;
  return reduceNumber(personalMonth + reduceNumber(date, false), false);
}

function attitudeNumber(dob: string) {
  if (!dob) return 0;
  const [_, m, d] = dob.split("-").map(Number);
  if (!m || !d) return 0;
  return reduceNumber(m + d, true);
}

function birthdayNumber(dob: string) {
  if (!dob) return 0;
  const day = Number(dob.split("-")[2]);
  if (!day) return 0;
  return reduceNumber(day, true);
}

function formatNumberLabel(n: number) {
  if (!n) return "—";
  if (n === 11 || n === 22 || n === 33) return `Master ${n}`;
  return `${n}`;
}

function buildMonthlyNarrative({ lifePath, personalYear, personalMonth, personalDay }: { lifePath: number; personalYear: number; personalMonth: number; personalDay: number; }) {
  const lp = CORE_NUMBERS[(lifePath > 9 ? reduceNumber(lifePath, false) : lifePath) as keyof typeof CORE_NUMBERS] || CORE_NUMBERS[1];
  const py = CORE_NUMBERS[(personalYear > 9 ? reduceNumber(personalYear, false) : personalYear) as keyof typeof CORE_NUMBERS] || CORE_NUMBERS[1];
  const pm = CORE_NUMBERS[(personalMonth > 9 ? reduceNumber(personalMonth, false) : personalMonth) as keyof typeof CORE_NUMBERS] || CORE_NUMBERS[1];
  const pd = CORE_NUMBERS[(personalDay > 9 ? reduceNumber(personalDay, false) : personalDay) as keyof typeof CORE_NUMBERS] || CORE_NUMBERS[1];

  return {
    title: `${lp.archetype} energy meets a ${py.archetype.toLowerCase()} year`,
    intro: `This month asks you to move with more intention than pressure. Your Life Path emphasizes ${lp.keywords.join(", ")}, while your Personal Year highlights ${py.keywords.join(", ")}. Instead of reacting to everything at once, choose what deserves compounding effort and let the rest stay small.`,
    relationships: `In relationships, the month rewards emotional precision. ${lp.love} With a ${formatNumberLabel(personalMonth)} Personal Month, conversations are most productive when they are specific, calm, and timely.`,
    work: `Career and money improve when your calendar reflects your priorities. ${py.career} Your ${formatNumberLabel(personalDay)} Personal Day energy is a cue for the tone to set right now: ${pd.strengths}`,
    lesson: `Your deeper lesson is simple: ${pm.caution} This is a month to stop mistaking busyness for destiny and start aligning your energy with what actually grows your life.`,
  };
}

function getLuckyDates(personalMonth: number) {
  const base = personalMonth > 9 ? reduceNumber(personalMonth, false) : personalMonth;
  return [base, base + 9, base + 18].map((n) => ((n - 1) % 28) + 1);
}

function getChallengeDates(personalYear: number) {
  const base = personalYear > 9 ? reduceNumber(personalYear, false) : personalYear;
  return [base + 3, base + 11, base + 19].map((n) => ((n - 1) % 28) + 1);
}

const menu = ["Daily", "Love", "Compatibility", "Career", "Numerology", "Yearly", "Guides"];

export default function NumerologyMonthlySiteMockup() {
  const [name, setName] = useState("Ava Morgan");
  const [dob, setDob] = useState("1990-09-04");
  const now = new Date();
  const [selectedMonth, setSelectedMonth] = useState(now.getMonth());
  const [selectedYear, setSelectedYear] = useState(now.getFullYear());

  const numbers = useMemo(() => {
    const lp = lifePathFromDob(dob);
    const py = personalYearNumber(dob, selectedMonth, selectedYear);
    const pm = personalMonthNumber(py, selectedMonth);
    const pd = personalDayNumber(pm, now.getDate());
    const att = attitudeNumber(dob);
    const bd = birthdayNumber(dob);
    const copy = buildMonthlyNarrative({ lifePath: lp, personalYear: py, personalMonth: pm, personalDay: pd });

    return {
      lifePath: lp,
      personalYear: py,
      personalMonth: pm,
      personalDay: pd,
      attitude: att,
      birthday: bd,
      copy,
      luckyDates: getLuckyDates(pm),
      challengeDates: getChallengeDates(py),
    };
  }, [dob, selectedMonth, selectedYear]);

  const highlighted = CORE_NUMBERS[(numbers.personalMonth > 9 ? reduceNumber(numbers.personalMonth, false) : numbers.personalMonth) as keyof typeof CORE_NUMBERS] || CORE_NUMBERS[1];

  return (
    <div className="min-h-screen bg-stone-50 text-stone-900">
      <header className="border-b bg-white/90 backdrop-blur sticky top-0 z-40">
        <div className="mx-auto max-w-7xl px-4 py-4 flex flex-col gap-4 md:flex-row md:items-center md:justify-between">
          <div>
            <div className="flex items-center gap-2">
              <Sparkles className="h-5 w-5" />
              <span className="font-semibold tracking-wide">Numera</span>
            </div>
            <p className="text-sm text-stone-600">Modern numerology with clearer timing, better structure, and daily relevance.</p>
          </div>
          <nav className="flex flex-wrap gap-2">
            {menu.map((item) => (
              <Button key={item} variant={item === "Numerology" ? "default" : "outline"} className="rounded-2xl">{item}</Button>
            ))}
          </nav>
        </div>
      </header>

      <main className="mx-auto max-w-7xl px-4 py-8">
        <div className="grid gap-6 lg:grid-cols-[1.6fr_0.8fr]">
          <section className="space-y-6">
            <motion.div initial={{ opacity: 0, y: 12 }} animate={{ opacity: 1, y: 0 }} transition={{ duration: 0.35 }}>
              <Card className="rounded-3xl border-none shadow-lg bg-white">
                <CardContent className="p-6 md:p-8 space-y-6">
                  <div className="flex flex-col gap-6 xl:flex-row xl:items-end xl:justify-between">
                    <div className="space-y-3">
                      <Badge className="rounded-full px-3 py-1 text-sm">Monthly Numerology</Badge>
                      <h1 className="text-3xl md:text-5xl font-semibold tracking-tight">{name || "Your"} Monthly Numerology Forecast</h1>
                      <p className="text-stone-600 max-w-3xl text-base md:text-lg">
                        A cleaner, more actionable alternative to generic horoscope pages — centered on your Personal Year, Month, Day, and core numerology blueprint.
                      </p>
                    </div>
                    <div className="grid grid-cols-2 gap-3 min-w-[280px]">
                      <div className="space-y-2">
                        <label className="text-sm text-stone-600">Full name</label>
                        <Input value={name} onChange={(e) => setName(e.target.value)} className="rounded-2xl" />
                      </div>
                      <div className="space-y-2">
                        <label className="text-sm text-stone-600">Birth date</label>
                        <Input type="date" value={dob} onChange={(e) => setDob(e.target.value)} className="rounded-2xl" />
                      </div>
                    </div>
                  </div>

                  <div className="flex flex-wrap items-center gap-2">
                    {MONTH_NAMES.map((m, idx) => (
                      <Button
                        key={m}
                        variant={selectedMonth === idx ? "default" : "outline"}
                        className="rounded-full"
                        onClick={() => setSelectedMonth(idx)}
                      >
                        {m.slice(0, 3)}
                      </Button>
                    ))}
                  </div>

                  <div className="grid gap-4 sm:grid-cols-2 xl:grid-cols-5">
                    {[
                      ["Life Path", formatNumberLabel(numbers.lifePath), <UserCircle2 className="h-4 w-4" key="a" />],
                      ["Personal Year", formatNumberLabel(numbers.personalYear), <CalendarDays className="h-4 w-4" key="b" />],
                      ["Personal Month", formatNumberLabel(numbers.personalMonth), <Clock3 className="h-4 w-4" key="c" />],
                      ["Personal Day", formatNumberLabel(numbers.personalDay), <Star className="h-4 w-4" key="d" />],
                      ["Attitude", formatNumberLabel(numbers.attitude), <BarChart3 className="h-4 w-4" key="e" />],
                    ].map(([label, value, icon]) => (
                      <Card key={String(label)} className="rounded-3xl shadow-sm">
                        <CardContent className="p-4">
                          <div className="flex items-center justify-between text-stone-500 text-sm">
                            <span>{label}</span>
                            {icon}
                          </div>
                          <div className="mt-3 text-3xl font-semibold">{value}</div>
                        </CardContent>
                      </Card>
                    ))}
                  </div>
                </CardContent>
              </Card>
            </motion.div>

            <Tabs defaultValue="monthly" className="space-y-4">
              <TabsList className="flex w-full flex-wrap h-auto rounded-2xl bg-stone-200/70 p-1 justify-start">
                <TabsTrigger value="yesterday" className="rounded-xl">Yesterday</TabsTrigger>
                <TabsTrigger value="today" className="rounded-xl">Today</TabsTrigger>
                <TabsTrigger value="tomorrow" className="rounded-xl">Tomorrow</TabsTrigger>
                <TabsTrigger value="weekly" className="rounded-xl">Weekly</TabsTrigger>
                <TabsTrigger value="monthly" className="rounded-xl">Monthly</TabsTrigger>
                <TabsTrigger value="yearly" className="rounded-xl">Yearly</TabsTrigger>
              </TabsList>

              <TabsContent value="monthly">
                <Card className="rounded-3xl shadow-md">
                  <CardHeader className="pb-2">
                    <div className="flex flex-wrap items-center gap-3">
                      <CardTitle className="text-2xl md:text-3xl">{MONTH_NAMES[selectedMonth]} {selectedYear}</CardTitle>
                      <Badge variant="outline" className="rounded-full">{numbers.copy.title}</Badge>
                    </div>
                  </CardHeader>
                  <CardContent className="space-y-5 text-[15px] leading-7 text-stone-700">
                    <p>{numbers.copy.intro}</p>
                    <p>{numbers.copy.relationships}</p>
                    <p>{numbers.copy.work}</p>
                    <p>{numbers.copy.lesson}</p>
                    <Separator />
                    <div className="grid gap-4 md:grid-cols-2">
                      <div className="rounded-2xl bg-stone-100 p-4">
                        <div className="font-medium">Standout dates</div>
                        <p className="mt-2 text-stone-600">{numbers.luckyDates.join(", ")}</p>
                      </div>
                      <div className="rounded-2xl bg-stone-100 p-4">
                        <div className="font-medium">Use extra awareness on</div>
                        <p className="mt-2 text-stone-600">{numbers.challengeDates.join(", ")}</p>
                      </div>
                    </div>
                  </CardContent>
                </Card>
              </TabsContent>

              {[
                ["today", "Today", `Your Personal Day ${formatNumberLabel(numbers.personalDay)} favors ${highlighted.keywords.join(", ")}. ${highlighted.strengths}`],
                ["tomorrow", "Tomorrow", `Tomorrow keeps the same monthly theme but asks for better pacing. ${highlighted.caution}`],
                ["weekly", "This Week", `This week is best used for compound actions rather than emotional reaction. Schedule what matters, remove what leaks energy, and let your numbers shape your timing.`],
                ["yearly", "This Year", `Your ${formatNumberLabel(numbers.personalYear)} Personal Year is the larger story. It shapes the kinds of opportunities, tests, and growth edges you will keep meeting until year-end.`],
                ["yesterday", "Yesterday", `Yesterday likely surfaced a clue about your current cycle: what drained you, what expanded you, and where your energy naturally wanted to go.`],
              ].map(([value, title, text]) => (
                <TabsContent key={String(value)} value={String(value)}>
                  <Card className="rounded-3xl shadow-md">
                    <CardHeader>
                      <CardTitle>{title}</CardTitle>
                    </CardHeader>
                    <CardContent className="text-stone-700 leading-7">{text}</CardContent>
                  </Card>
                </TabsContent>
              ))}
            </Tabs>

            <div className="grid gap-4 md:grid-cols-2 xl:grid-cols-3">
              {[
                ["Compatibility", "See how your numbers interact in love, friendship, and business.", HeartHandshake],
                ["Yearly Forecast", "Map the next 12 months with your Personal Year and Month sequence.", CalendarDays],
                ["Name Energy", "Explore how expression, soul urge, and personality numbers shape identity.", Sparkles],
              ].map(([title, copy, Icon]) => (
                <Card key={String(title)} className="rounded-3xl shadow-sm">
                  <CardContent className="p-5 space-y-3">
                    <Icon className="h-5 w-5" />
                    <div className="text-lg font-semibold">{title}</div>
                    <p className="text-sm text-stone-600">{copy}</p>
                    <Button variant="ghost" className="px-0">Explore <ChevronRight className="h-4 w-4 ml-1" /></Button>
                  </CardContent>
                </Card>
              ))}
            </div>
          </section>

          <aside className="space-y-4">
            <Card className="rounded-3xl shadow-sm">
              <CardHeader>
                <CardTitle className="text-xl">Why this feels more accurate</CardTitle>
              </CardHeader>
              <CardContent className="space-y-3 text-sm leading-6 text-stone-600">
                <p>It uses your actual birth date to calculate core and timing numbers instead of giving one generic reading to every person in the same sign.</p>
                <p>It separates long-cycle themes from month and day tone, so the reading feels more specific and actionable.</p>
                <p>The interface is designed for repeat visits: daily insight, monthly forecast, yearly arc, and related compatibility tools.</p>
              </CardContent>
            </Card>

            <Card className="rounded-3xl shadow-sm">
              <CardHeader>
                <CardTitle className="text-xl">This month’s core tone</CardTitle>
              </CardHeader>
              <CardContent className="space-y-3">
                <Badge className="rounded-full">{highlighted.archetype}</Badge>
                <p className="text-sm text-stone-600">{highlighted.strengths}</p>
                <p className="text-sm text-stone-600">Watch for: {highlighted.caution}</p>
              </CardContent>
            </Card>

            <Card className="rounded-3xl shadow-sm">
              <CardHeader>
                <CardTitle className="text-xl">Numerology system</CardTitle>
              </CardHeader>
              <CardContent className="text-sm text-stone-600 space-y-2">
                <div className="flex justify-between"><span>Birthday Number</span><span className="font-medium text-stone-900">{formatNumberLabel(numbers.birthday)}</span></div>
                <div className="flex justify-between"><span>Attitude Number</span><span className="font-medium text-stone-900">{formatNumberLabel(numbers.attitude)}</span></div>
                <div className="flex justify-between"><span>Life Path</span><span className="font-medium text-stone-900">{formatNumberLabel(numbers.lifePath)}</span></div>
                <div className="flex justify-between"><span>Personal Year</span><span className="font-medium text-stone-900">{formatNumberLabel(numbers.personalYear)}</span></div>
                <div className="flex justify-between"><span>Personal Month</span><span className="font-medium text-stone-900">{formatNumberLabel(numbers.personalMonth)}</span></div>
              </CardContent>
            </Card>
          </aside>
        </div>
      </main>
    </div>
  );
}
