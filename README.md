// App.js
import React, { useState } from 'react';
import { View, Text, TouchableOpacity, ScrollView, TextInput, Image, StyleSheet } from 'react-native';
import * as ImagePicker from 'expo-image-picker';
import { firestore, storage } from './firebase';

const ChecklistScreen = () => {
  const [checklist, setChecklist] = useState({
    motorista: '',
    chassi: '',
    ferramentas: {},
    fotos: {},
    combustivel: { diesel: 0, arla: 0 }
  });

  // Função para selecionar fotos
  const selecionarFoto = async (item) => {
    let result = await ImagePicker.launchImageLibraryAsync({
      mediaTypes: ImagePicker.MediaTypeOptions.Images,
      quality: 0.8,
    });

    if (!result.canceled) {
      setChecklist(prev => ({
        ...prev,
        fotos: { ...prev.fotos, [item]: result.assets[0].uri }
      }));
    }
  };

  // Salvar no Firebase
  const salvarChecklist = async () => {
    try {
      const docRef = await firestore.collection('checklists').add(checklist);
      console.log('Checklist salvo com ID:', docRef.id);
    } catch (e) {
      console.error('Erro ao salvar:', e);
    }
  };

  return (
    <ScrollView style={styles.container}>
      {/* Cabeçalho */}
      <View style={styles.header}>
        <Text style={styles.titulo}>VehicleCheck Pro</Text>
        <Text style={styles.subtitulo}>Inspeção Veicular Completa</Text>
      </View>

      {/* Seção de Identificação */}
      <View style={styles.secao}>
        <TextInput
          style={styles.input}
          placeholder="Nome do Motorista"
          onChangeText={text => setChecklist({ ...checklist, motorista: text })}
        />
        <TextInput
          style={styles.input}
          placeholder="Chassi do Veículo"
          keyboardType="numeric"
          onChangeText={text => setChecklist({ ...checklist, chassi: text })}
        />
      </View>

      {/* Ferramentas */}
      <View style={styles.secao}>
        <Text style={styles.tituloSecao}>Ferramentas Obrigatórias</Text>
        {['Macaco', 'Triângulo', 'Chave de Roda'].map((item) => (
          <View key={item} style={styles.itemRow}>
            <Text style={styles.itemTexto}>{item}</Text>
            
            <TouchableOpacity 
              style={[styles.botao, checklist.ferramentas[item] === 'SIM' && styles.botaoSim]}
              onPress={() => setChecklist(prev => ({
                ...prev,
                ferramentas: { ...prev.ferramentas, [item]: 'SIM' }
              }))}
            >
              <Text>✅ SIM</Text>
            </TouchableOpacity>

            <TouchableOpacity 
              style={[styles.botao, checklist.ferramentas[item] === 'NÃO' && styles.botaoNao]}
              onPress={() => setChecklist(prev => ({
                ...prev,
                ferramentas: { ...prev.ferramentas, [item]: 'NÃO' }
              }))}
            >
              <Text>❌ NÃO</Text>
            </TouchableOpacity>

            {checklist.ferramentas[item] === 'SIM' && (
              <TouchableOpacity 
                style={styles.botaoFoto}
                onPress={() => selecionarFoto(item)}
              >
                <Text>📷 Foto</Text>
              </TouchableOpacity>
            )}
          </View>
        ))}
      </View>

      {/* Botão de Salvamento */}
      <TouchableOpacity 
        style={styles.botaoSalvar}
        onPress={salvarChecklist}
      >
        <Text style={styles.textoBotao}>💾 Salvar Checklist</Text>
      </TouchableOpacity>
    </ScrollView>
  );
};

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: '#F8F9FA' },
  header: { padding: 20, backgroundColor: '#FFD700', marginBottom: 15 },
  titulo: { fontSize: 28, fontWeight: 'bold', color: '#2C3E50' },
  subtitulo: { fontSize: 16, color: '#34495E' },
  secao: { margin: 15, padding: 15, backgroundColor: 'white', borderRadius: 10 },
  tituloSecao: { fontSize: 18, fontWeight: '600', marginBottom: 10 },
  itemRow: { flexDirection: 'row', alignItems: 'center', marginVertical: 8 },
  itemTexto: { flex: 1, marginRight: 10 },
  botao: { 
    padding: 10, 
    marginHorizontal: 5,
    borderRadius: 8,
    borderWidth: 1,
    borderColor: '#DDD'
  },
  botaoSim: { backgroundColor: '#27AE60', borderColor: '#27AE60' },
  botaoNao: { backgroundColor: '#E74C3C', borderColor: '#E74C3C' },
  botaoFoto: { 
    backgroundColor: '#3498DB', 
    padding: 10,
    borderRadius: 8 
  },
  botaoSalvar: {
    backgroundColor: '#2C3E50',
    padding: 15,
    margin: 20,
    borderRadius: 10,
    alignItems: 'center'
  },
  textoBotao: { color: 'white', fontWeight: 'bold' },
  input: {
    height: 40,
    borderColor: '#DDD',
    borderWidth: 1,
    marginBottom: 15,
    padding: 10,
    borderRadius: 8
  }
});

export default ChecklistScreen;
