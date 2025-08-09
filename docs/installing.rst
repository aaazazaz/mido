from mido import Message, MidiFile, MidiTrack, MetaMessage, bpm2tempo

mid = MidiFile(type=1)
tempo = bpm2tempo(76)

# ===== 主旋律轨 =====
melody = MidiTrack()
mid.tracks.append(melody)
melody.append(MetaMessage('set_tempo', tempo=tempo))
melody.append(Message('program_change', program=0, time=0))

# 旋律音符
melody_notes = [
    (60, 1), (62, 1), (64, 1), (65, 1),
    (67, 1), (65, 1), (64, 2),
    (62, 1), (64, 1), (65, 1), (67, 1),
    (69, 1), (67, 1), (65, 2),

    (64, 1), (65, 1), (67, 1), (69, 1),
    (71, 1), (69, 1), (67, 2),
    (65, 1), (67, 1), (69, 1), (71, 1),
    (72, 1), (71, 1), (69, 2),

    (67, 1), (69, 1), (71, 1), (72, 1),
    (74, 1), (72, 1), (71, 2),
    (69, 1), (71, 1), (72, 1), (74, 1),
    (76, 1), (74, 1), (72, 2),

    (72, 1), (71, 1), (69, 1), (67, 1),
    (65, 1), (64, 1), (62, 2),
]

# 歌词与旋律一一对应（空格表示延音）
lyrics = list("滨城碧水映书香 首师薪火耀东方 成德修身立志远 达才逐梦赋华章 "
              "成德达才赋能未来 青春热血澎湃澎湃 同心共进笑迎朝阳 滨中学子飞向苍穹 "
              "春风化雨润心田 桃李芬芳满人间 学海乘舟勇破浪 滨中荣光照万年")

# 添加音符和歌词
lyric_index = 0
for note, length in melody_notes:
    melody.append(Message('note_on', note=note, velocity=90, time=0))
    melody.append(MetaMessage('lyrics', text=lyrics[lyric_index], time=0))
    melody.append(Message('note_off', note=note, velocity=90, time=int(length * 480)))
    lyric_index = min(lyric_index + 1, len(lyrics) - 1)

# ===== 和弦伴奏轨 =====
chords = MidiTrack()
mid.tracks.append(chords)
chords.append(Message('program_change', program=48, time=0))
chord_progression = [
    [60, 64, 67], [65, 69, 72], [67, 71, 74], [60, 64, 67],
] * 8
for chord in chord_progression:
    for note in chord:
        chords.append(Message('note_on', note=note, velocity=60, time=0))
    for note in chord:
        chords.append(Message('note_off', note=note, velocity=60, time=960))

# ===== 低音轨 =====
bass = MidiTrack()
mid.tracks.append(bass)
bass.append(Message('program_change', program=32, time=0))
bass_notes = [48, 53, 55, 48] * 8
for note in bass_notes:
    bass.append(Message('note_on', note=note, velocity=70, time=0))
    bass.append(Message('note_off', note=note, velocity=70, time=960))

# ===== 鼓点轨 =====
drums = MidiTrack()
mid.tracks.append(drums)
for _ in range(32):
    drums.append(Message('note_on', note=38, velocity=60, time=480, channel=9))
    drums.append(Message('note_off', note=38, velocity=60, time=0, channel=9))
    drums.append(Message('note_on', note=38, velocity=60, time=960, channel=9))
    drums.append(Message('note_off', note=38, velocity=60, time=0, channel=9))

# 保存
mid.save("shoushibinzhong_school_song_with_lyrics.mid")
print("✅ 带歌词的 MIDI 文件已生成：shoushibinzhong_school_song_with_lyrics.mid")
